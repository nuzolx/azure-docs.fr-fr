---
title: Débogage interactif avec Visual Studio Code
titleSuffix: Azure Machine Learning
description: Déboguer du code, des pipelines et des déploiements Azure Machine Learning de manière interactive à l’aide de Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
author: luisquintanilla
ms.author: luquinta
ms.date: 08/06/2020
ms.openlocfilehash: 73cb8396876a5baad74190ec9a86237362037c36
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87907100"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Débogage interactif avec Visual Studio Code

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Découvrez comment déboguer des pipelines et des déploiements Azure Machine Learning de manière interactive à l’aide de Visual Studio Code (VS Code) et [depugpy](https://github.com/microsoft/debugpy/).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Déboguer et résoudre les problèmes de pipelines de machine learning

Dans certains cas, vous devrez peut-être déboguer interactivement le code Python utilisé dans votre pipeline ML. À l’aide de VS Code et de debugpy, vous pouvez attacher le code au fur et à mesure de son exécution dans l’environnement d’apprentissage.

### <a name="prerequisites"></a>Prérequis

* Un __espace de travail Azure Machine Learning__ configuré pour utiliser un __réseau virtuel Azure__.
* un __pipeline Azure Machine Learning__ utilisant des scripts Python dans le cadre de ses étapes. Par exemple, PythonScriptStep.
* Un cluster de Capacité de calcul Machine Learning se trouvant __dans le réseau virtuel__, que __le pipeline utilise pour effectuer l’apprentissage__.
* Un __environnement de développement__ se trouvant __dans le réseau virtuel__. L’environnement de développement peut être l’une des ressources suivantes :

  * une machine virtuelle Azure se trouvant dans le réseau virtuel ;
  * une instance de calcul de machine virtuelle Notebook se trouvant dans le réseau virtuel ;
  * une machine client disposant d’une connectivité réseau privée au réseau virtuel, par le biais d’un VPN ou via ExpressRoute.

Pour plus d’informations sur l’utilisation d’un réseau virtuel Azure avec Azure Machine Learning, voir [Sécuriser l’expérimentation Azure Machine Learning et les travaux d’inférence au sein d’un réseau virtuel Azure](how-to-enable-virtual-network.md).

> [!TIP]
> Bien qu’il soit possible de travailler avec des ressources Azure Machine Learning qui ne se trouvent pas derrière un réseau virtuel, il est recommandé d’utiliser un réseau virtuel.

### <a name="how-it-works"></a>Fonctionnement

Les étapes de votre pipeline ML exécutent des scripts Python. Ces scripts sont modifiés pour effectuer les actions suivantes :

1. journaliser l’adresse IP de l’hôte sur lequel ils s’exécutent. Vous utilisez l’adresse IP pour connecter le débogueur au script.

2. Démarrez le composant de débogage debugpy et attendez qu’un débogueur se connecte.

3. À partir de votre environnement de développement, vous surveillez les journaux créés par le processus d’apprentissage pour trouver l’adresse IP où le script est en cours d’exécution.

4. Vous indiquez à VS Code l’adresse IP à laquelle connecter le débogueur à l’aide d’un fichier `launch.json`.

5. Vous attachez le débogueur et parcourez le script de manière interactive.

### <a name="configure-python-scripts"></a>Configurer des scripts Python

Pour activer le débogage, apportez les modifications suivantes au(x) script(s) Python utilisé(s) dans les étapes de votre pipeline ML :

1. Ajoutez les instructions d’importation suivantes :

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Ajoutez les arguments suivants : Ces arguments vous permettent d’activer le débogueur en fonction des besoins, et de définir le délai d’attente pour l’attachement du débogueur :

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Ajoutez les instructions suivantes. Ces instructions chargent le contexte d’exécution actuel afin que vous puissiez journaliser l’adresse IP du nœud sur lequel le code s’exécute :

    ```python
    global run
    run = Run.get_context()
    ```

1. Ajoutez une instruction `if` qui démarre debugpy et attend qu’un débogueur s’attache. Si aucun débogueur ne s’attache avant le délai d’expiration, le script continue à s’exécuter normalement. Veillez à remplacer les valeurs `HOST` et `PORT` dans la fonction `listen` par les vôtres.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

L’exemple Python suivant montre un fichier `train.py` de base qui active le débogage :

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Configurer un pipeline ML

Pour fournir les packages Python nécessaires pour démarrer debugpy et obtenir le contexte d’exécution, créez un environnement et définissez `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']`. Modifiez la version du kit de développement logiciel (SDK) pour qu’elle corresponde à celle que vous utilisez. L’extrait de code suivant montre comment créer un environnement :

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

Dans la section [Configurer des scripts Python](#configure-python-scripts), de nouveaux arguments ont été ajoutés aux scripts qu’utilisent les étapes de votre pipeline ML. L’extrait de code suivant montre comment utiliser ces arguments pour activer le débogage pour le composant et définir un délai d’expiration. Il montre également comment utiliser l’environnement créé précédemment en définissant `runconfig=run_config` :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Lorsque le pipeline s’exécute, chaque étape crée une exécution enfant. Si le débogage est activé, le script modifié journalise des informations similaires au texte suivant dans le fichier `70_driver_log.txt` pour l’exécution enfant :

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Enregistrez la valeur `ip_address`. Elles seront utilisées dans la section suivante.

> [!TIP]
> Vous pouvez également trouver l’adresse IP dans les journaux de l’exécution enfant pour cette étape de pipeline. Pour plus d’informations sur l’affichage de ces informations, voir [Surveiller les exécutions et les métriques des expériences Azure Machine Learning](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Configurer l’environnement de développement

1. Pour installer debugpy sur votre environnement de développement VS Code, utilisez la commande suivante :

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Pour plus d’informations sur l’utilisation de debugpy avec VS Code, consultez [Débogage à distance](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Pour configurer VS Code afin de communiquer avec la Capacité de calcul Azure Machine Learning qui exécute le débogueur, créez une nouvelle configuration de débogage :

    1. Dans VS Code, sélectionnez le menu __Déboguer__, puis sélectionnez __Ouvrir les configurations__. Un fichier nommé __launch.json__ s’ouvre.

    1. Dans le fichier __launch.json__, recherchez la ligne contenant `"configurations": [` et insérez le texte suivant après celle-ci : Remplacez l’entrée `"host": "<IP-ADDRESS>"` par l’adresse IP renvoyée dans vos journaux de la section précédente. Remplacez l’entrée `"localRoot": "${workspaceFolder}/code/step"` par un répertoire local contenant une copie du script en cours de débogage :

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Si la section des configurations contient déjà d’autres entrées, ajoutez une virgule (,) après le code que vous avez inséré.

        > [!TIP]
        > La meilleure pratique, en particulier pour les pipelines, consiste à conserver les ressources des scripts dans des répertoires distincts, de sorte que le code s’applique uniquement à chacune des étapes. Dans cet exemple, l’exemple de valeur `localRoot` fait référence à `/code/step1`.
        >
        > Si vous déboguez plusieurs scripts, dans des répertoires différents, créez une section de configuration distincte pour chaque script.

    1. Enregistrez le fichier __launch.json__.

### <a name="connect-the-debugger"></a>Connectez le débogueur.

1. Ouvrez VS Code et ouvrez une copie locale du script.
2. Définissez des points d’arrêt là où vous souhaitez que le script s’arrête une fois que vous l’avez attaché.
3. Pendant que le processus enfant exécute le script et que le `Timeout for debug connection` s’affiche dans les journaux, utilisez la touche F5 ou sélectionnez __Déboguer__. Lorsque vous y êtes invité, sélectionnez la configuration __Capacité de calcul Azure Machine Learning : débogage à distance__. Vous pouvez également sélectionner l’icône de débogage dans la barre latérale, l’entrée __Azure Machine Learning : débogage à distance__ dans le menu déroulant Débogage, puis utiliser la flèche verte pour attacher le débogueur.

    À ce stade, VS Code se connecte à debugpy sur le nœud de calcul et s’arrête au point d’arrêt que vous avez préalablement défini. Vous pouvez maintenant parcourir le code au fur et à mesure de son exécution, afficher des variables etc.

    > [!NOTE]
    > Si le journal contient une entrée indiquant `Debugger attached = False`, cela signifie que le délai d’expiration a expiré et que l’exécution du script a continué sans le débogueur. Soumettez à nouveau le pipeline et connectez le débogueur après le message `Timeout for debug connection`, et avant la fin du délai d’expiration.

## <a name="debug-and-troubleshoot-deployments"></a>Déboguer et résoudre les problèmes de déploiement

Dans certains cas, vous devrez peut-être déboguer interactivement le code Python contenu dans votre modèle de déploiement. Par exemple, si le script d’entrée échoue pour une raison ne pouvant pas être déterminée par une journalisation supplémentaire. À l’aide de VS Code et de debugpy, vous pouvez attacher au code qui s’exécute dans le conteneur Docker.

> [!IMPORTANT]
> Cette méthode de débogage ne fonctionne pas lorsque `Model.deploy()` et `LocalWebservice.deploy_configuration` sont utilisés pour déployer un modèle localement. Au lieu de cela, vous devez créer une image à l’aide de la méthode [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-).

Les déploiements de service web locaux nécessitent l’installation d’un Docker de travail sur votre système local. Pour plus d’informations sur l’utilisation de Docker, consultez la [documentation Docker](https://docs.docker.com/). Notez que, lorsque vous travaillez avec des instances de calcul, Docker est déjà installé.

### <a name="configure-development-environment"></a>Configurer l’environnement de développement

1. Pour installer debugpy sur votre environnement de développement VS Code local, utilisez la commande suivante :

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Pour plus d’informations sur l’utilisation de debugpy avec VS Code, consultez [Débogage à distance](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Pour configurer VS Code pour communiquer avec l’image Docker, créez une nouvelle configuration de débogage :

    1. Dans VS Code, sélectionnez le menu __Déboguer__, puis sélectionnez __Ouvrir les configurations__. Un fichier nommé __launch.json__ s’ouvre.

    1. Dans le fichier __launch.json__, recherchez la ligne qui contient `"configurations": [` et insérez le texte suivant après celle-ci :

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Si la section des configurations contient déjà d’autres entrées, ajoutez une virgule (,) après le code que vous avez inséré.

        Cette section est attachée au conteneur Docker à l’aide du port 5678.

    1. Enregistrez le fichier __launch.json__.

### <a name="create-an-image-that-includes-debugpy"></a>Créer une image qui inclut debugpy

1. Modifiez l’environnement Conda pour votre déploiement afin qu’il inclue debugpy. L’exemple suivant illustre son ajout à l’aide du paramètre `pip_packages` :

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Pour démarrer debugpy et attendre une connexion au démarrage du service, ajoutez le code suivant au début de votre fichier `score.py` :

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Créez une image basée sur la définition de l’environnement et extrayez l’image dans le registre local. 

    > [!NOTE]
    > Cet exemple suppose que `ws` pointe vers votre espace de travail Azure Machine Learning et que `model` est le modèle en cours de déploiement. Le fichier `myenv.yml` contient les dépendances Conda créées à l’étape 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Une fois l’image créée et téléchargée, le chemin de l’image (y compris le référentiel, le nom et la balise, qui dans ce cas est également son code de hachage) s’affiche dans un message semblable au suivant :

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Pour faciliter le travail sur l’image, utilisez la commande suivante pour ajouter une balise. Remplacez `myimagepath` par la valeur d’emplacement de l’étape précédente.

    ```bash
    docker tag myimagepath debug:1
    ```

    Pour le reste des étapes, vous pouvez faire référence à l’image locale en tant que `debug:1` au lieu de la valeur du chemin complet de l’image.

### <a name="debug-the-service"></a>Déboguer le service

> [!TIP]
> Si vous définissez un délai d’expiration pour la connexion de debugpy dans le fichier `score.py`, vous devez connecter VS Code à la session de débogage avant l’expiration du délai. Démarrez VS Code, ouvrez la copie locale de `score.py`, définissez un point d’arrêt et préparez-le avant d’utiliser les étapes de cette section.
>
> Pour plus d’informations sur le débogage et la définition de points d’arrêt, consultez [Débogage](https://code.visualstudio.com/Docs/editor/debugging).

1. Pour démarrer un conteneur Docker à l’aide de l’image, utilisez la commande suivante :

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_path_to_score.py>:/var/azureml-apps/score.py debug:1 /bin/bash
    ```

    Cela permet de joindre votre `score.py` localement à celui du conteneur. Par conséquent, toute modification effectuée dans l’éditeur est automatiquement répercutée dans le conteneur.

1. Dans le conteneur, exécutez la commande suivante dans l’interpréteur de commandes.

    ```bash
    runsvdir /var/runit
    ```

1. Pour attacher VS Code à debugpy à l’intérieur du conteneur, ouvrez VS Code et utilisez la touche F5 ou sélectionnez __Déboguer__. Lorsque vous y êtes invité, sélectionnez la configuration __Déploiement Azure Machine Learning : Configuration du débogage Docker__. Vous pouvez également sélectionner l’icône de débogage dans la barre latérale, l’entrée __Déploiement Azure Machine Learning : entrée de débogage__ Docker dans le menu déroulant Déboguer, puis utiliser la flèche verte pour attacher le débogueur.

    ![Icône de débogage, bouton Démarrer le débogage et sélecteur de configuration](./media/how-to-troubleshoot-deployment/start-debugging.png)

À ce stade, VS Code se connecte à debugpy à l’intérieur du conteneur Docker et s’arrête au point d’arrêt que vous avez préalablement défini. Vous pouvez maintenant parcourir le code au fur et à mesure de son exécution, afficher des variables etc.

Pour plus d’informations sur l’utilisation de VS Code pour déboguer Python, consultez [Déboguer votre code Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

### <a name="stop-the-container"></a>Arrêter le conteneur

Pour arrêter un conteneur, utilisez la commande suivante :

```bash
docker stop debug
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré Visual Studio Code Remote, vous pouvez utiliser une instance de calcul comme calcul à distance à partir de Visual Studio Code pour déboguer votre code de manière interactive. 

[Tutoriel : Effectuer l’apprentissage de votre premier modèle ML](tutorial-1st-experiment-sdk-train.md) montre comment utiliser une instance de calcul avec un notebook intégré.
