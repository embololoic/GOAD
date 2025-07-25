# Documentation d’Installation du Lab GOAD (Graph of Active Directory)

## 1. Prérequis

### Système hôte

* Windows 10 ou 11 (64 bits)

### Outils à installer

1. **Git Bash** : [https://gitforwindows.org/](https://gitforwindows.org/)
2. **VirtualBox** : [https://www.virtualbox.org/](https://www.virtualbox.org/)
3. **Vagrant** : [https://www.vagrantup.com/](https://www.vagrantup.com/)
4. **Python** (à partir de 3.8, idéalement 3.11) : [https://www.python.org/downloads/](https://www.python.org/downloads/)

### Variables d'environnement

Vérifiez que Python et Vagrant sont bien ajoutés dans la variable `PATH`. Pour `poetry` (optionnel), ajoutez manuellement sa variable d’environnement si nécessaire.

---

## 2. Récupération du projet GOAD

```bash
git clone https://github.com/Orange-Cyberdefense/GOAD.git
cd GOAD
```

---

## 3. Initialisation de l’environnement Python

### Script `goad.sh`

Le script `goad.sh` est fourni avec le projet. Il permet :

* de créer un environnement virtuel Python dans `~/.goad/.venv`
* d’installer les dépendances depuis `requirements.yml` ou `requirements_311.yml`
* de lancer `goad.py`

### Modification recommandée de `goad.sh`

Remplacez `source $venv/bin/activate` par `source $venv/Scripts/activate` si vous êtes sous Windows.

Ajoutez des logs pour faciliter le débogage :

```bash
echo "[INFO] Activation de l’environnement virtuel..."
echo "[INFO] Lancement de goad.py avec les arguments : $@"
```

### Exemple d'exécution dans Git Bash

```bash
./goad.sh
```

**Si une erreur `ModuleNotFoundError` survient (ex : `rich` ou `jinja2`) :**

```bash
source ~/.goad/.venv/Scripts/activate
pip install rich jinja2
```

---

## 4. Déploiement des machines virtuelles

### Chemin du Vagrantfile

```bash
cd GOAD/ad/GOAD/providers/virtualbox/
```

### Contenu du `Vagrantfile`

```ruby
Vagrant.configure("2") do |config|
  ip_range = "192.168.56"

  boxes = [
    { :name => "GOAD-DC01",  :ip => "#{ip_range}.10", :box => "StefanScherer/windows_2019", :box_version => "2021.05.15", :cpus => 2, :mem => 3000 },
    { :name => "GOAD-DC02",  :ip => "#{ip_range}.11", :box => "StefanScherer/windows_2019", :box_version => "2021.05.15", :cpus => 2, :mem => 3000 },
    { :name => "GOAD-DC03",  :ip => "#{ip_range}.12", :box => "StefanScherer/windows_2016", :box_version => "2017.12.14", :cpus => 2, :mem => 3000 },
    { :name => "GOAD-SRV02", :ip => "#{ip_range}.22", :box => "StefanScherer/windows_2019", :box_version => "2021.05.15", :cpus => 2, :mem => 6000 },
    { :name => "GOAD-SRV03", :ip => "#{ip_range}.23", :box => "StefanScherer/windows_2016", :box_version => "2019.02.14", :cpus => 2, :mem => 5000 }
  ]

  boxes.each do |opts|
    config.vm.define opts[:name] do |vm_config|
      vm_config.vm.box = opts[:box]
      vm_config.vm.box_version = opts[:box_version]
      vm_config.vm.hostname = opts[:name]
      vm_config.vm.network "private_network", ip: opts[:ip]
      vm_config.vm.provider "virtualbox" do |vb|
        vb.name = opts[:name]
        vb.cpus = opts[:cpus]
        vb.memory = opts[:mem]
      end
    end
  end
end
```

### Lancer les machines

```bash
vagrant up       # pour toutes les VMs
vagrant up GOAD-DC01   # pour une seule
```

### Nombre total de VMs

Il y a **5 machines virtuelles** à déployer selon la configuration fournie.

---

## 5. Mots de passe des VMs (par défaut)

* **Utilisateur** : `vagrant`
* **Mot de passe** : `vagrant`

---

## 6. Dépannage : WinRM Error

Erreur courante :

```
WSMAN ERROR CODE: 2147942421 - The device is not ready
```

Cela peut être causé par un démarrage lent ou une configuration WinRM incorrecte.

### Solution

Exécuter dans une session PowerShell Admin sur la VM :

```powershell
Set-Item -Path WSMan:\localhost\Service\AllowUnencrypted -Value $true
Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
Restart-Service WinRM
```

---

## 7. Capture illustrative (exemple)

```
GOAD
├── goad.sh
├── goad.py
├── ad
    ├── GOAD
        ├── providers
            └── virtualbox
                ├── Vagrantfile
                ├── inventory
```

---

## 8. Lancement de GOAD

Une fois les VMs déployées et Python prêt :

```bash
source ~/.goad/.venv/Scripts/activate
./goad.sh
```

---

Pour toute erreur supplémentaire, n’hésite pas à les noter dans une section "FAQ" à ajouter.
