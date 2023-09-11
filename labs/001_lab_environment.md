# Umgebung vorbereiten

Das Lab kann vollständig mit GitHub-Codespaces druchgeführt werden. Dafür bauchst du einen GitHub-Account.

**Wir empfehlen die Durchführung des Labs unbedingt mit GitHub Codespaces.** Dies aus folgenden Gründen:
- die Zeit für das Lab ist sehr kurz
- die Labs wurden mit GitHub Codespaces getestet
- Vermeidung von Installationsproblemen auf dem lokalen System (z.B. falsche Python Version, System Libraries fehlen, etc.)

Wer will, kann die Labs aber auch lokal durchführen. Eine detaillierte Installationsanleitung befindet sich im Kapitel [Lokale Umgebung aufsetzen](#lokale-umgebung-aufsetzen) weiter unten.

## GitHub-Repository

Es existiert ein Template Repository unter https://github.com/iimsand/mlops-techlab-digits-template. Dieses Repository dient als Startpunkt für dein eigenes Repository und enthält auch Lösungen zu jedem Lab, falls du einmal nicht weiterkommen solltest, kannst du die Dateien vom entsprechenden Branch kopieren. Das Repository ist schreibgeschützt und du musst dein eigenes Repository erstellen.

Dein Repository kannst du wie folgt erstellen:

1. "Use this template" aufklappen und "Create new repository" klicken:   
![GitHub create repo from template](screenshots/github_repo_create_from_template.png)
1. Repository name "digits" wählen und auf "Private" setzen:   
![GitHub create repo from template - info](screenshots/github_repo_create_from_template_info.png)

## GitHub-Codespace

Aus deinem neu erstellten Repository kannst du nun GitHub-Codespace öffnen.

1. Kontrolliere ob du dich auf deinem eigenen Repository befindest: https://github.com/<GIT_USER>/digits
1. Unter "Code -> Codespaces" kannst du nun mit "Create codespace on main" die Umgebung starten:   
![GitHub start codespace](screenshots/github_repo_start_codespace.png)

Weiter Informationen zu codespace: https://docs.github.com/en/codespaces/developing-in-codespaces/creating-a-codespace-for-a-repository

### Python konfigurieren

Bei einer lokalen Installation sollten wir eine virtuelle Python Umgebung nutzen, doch wenn wir mit GiHub-Codespace arbeiten, ist dies nicht nötig.

Doch müssen in GitHub-Codespace trotzdem noch ein paar Konfigurationen für Python vorgenommen werden. Alle Abhängigkeiten aus dem `requirements.txt` werden zwar automatisch installiert, doch wurden noch nicht alle Pfade korrekt gesetzt.

Dazu im Terminal die folgenden Befehle ausführen:

```shell
echo "export PATH=`python -m site --user-site`/bin:\$PATH" >> ~/.bashrc
echo "export PYTHONPATH=$CODESPACE_VSCODE_FOLDER" >> ~/.bashrc
```

Damit die Konfiguration aktiv wird, muss ein neues Terminal geöffnet werden.

## Lokale Umgebung aufsetzen

Dieses Kapitel musst du nur beachten, falls du das Lab in deiner lokalen Umgebung durchspielen möchtest. Wenn du Codespaces nutzt, kannst du dieses Kapitel komplett ignorieren.

Wir empfehlen Visual Studio Code als Entwicklungsumgebung. Die untenstehende Anleitung beschreibt die Schritte, um die Voraussetzungen in einer Linux-Umgebung bereitzustellen.

### Git

```shell
sudo apt-get install git
```

### Python

```shell
sudo apt-get update
sudo apt-get install python3 python3-pip python3.8-venv
```

### Virtuelle Umgebung für Python erstellen

Damit wir sicher die richtige Version der Python-Erweiterungen geladen haben, erstellen wir eine virtuelle Umgebung. So können wir in unterschiedlichen Python-Projekten verschiedene Versionen der Python-Erweiterungen nutzen. Siehe auch: https://docs.python.org/3/library/venv.html

**Für die lokale Entwicklung ist die virtuelle Umgebung wärmstens zu empfehlen!**

```shell
python3 -m venv .env
echo "export PYTHONPATH=$PWD" >> .env/bin/activate

# muss jedesmal nach dem Öffnen des Terminals ausgeführt werden
# danach erscheint (.env) am Anfang der Zeile
source .env/bin/activate

pip install --upgrade pip setuptools
pip install -r requirements.txt
```

### Visual Studio Code

Die offizielle Installationsanleitung ist unter https://code.visualstudio.com/docs/setup/linux zu finden.

Die folgenden Schritte werden darin beschrieben:

```shell
sudo apt-get install wget gpg
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
rm -f packages.microsoft.gpg

# Update the package cache and install Visuall Studio Code
sudo apt install apt-transport-https
sudo apt update
sudo apt install code
```

#### Visual Studio Code - Erweiterungen

Um optimal arbeiten zu können, installieren wir noch ein paar Erweiterungen.

```shell
code --install-extension ms-python.python
code --install-extension ms-toolsai.jupyter
code --install-extension Iterative.vscode-dvc-pack
```

### Docker

Die offizielle Installationsanleitung ist unter https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository zu finden.

Die folgenden Schritte werden darin beschrieben:

```shell
# Update the apt package index and install packages to allow apt to use a repository over HTTPS:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg

# Add Docker’s official GPG key:
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Use the following command to set up the repository:
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update the apt package index:
sudo apt-get update

# Install Docker Engine, containerd, and Docker Compose.
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Create the docker group.
sudo groupadd docker

# Add your user to the docker group.
sudo usermod -aG docker $USER
```

Am besten das System neu starten damit alle Änderungen übernommen werden.

Testen das `docker` funktioniert:

```shell
docker run hello-world
```
