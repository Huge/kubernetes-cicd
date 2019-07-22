# Kubernetes CI/CD ELOS projekt

Skripty a sablony pre vytvorenie CI/CD projektu.

## Azure AKS

### Deployment cez objednavku sluzby

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FHuge%2Fkubernetes-cicd%2Fmaster%2Fazure%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>
<!–– a href="http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FHuge%2Fkubernetes-cicd%2Fmaster%2Fazure%2Fazuredeploy.json" target="_blank">    <img src="http://armviz.io/visualizebutton.png"/></a> ––>

#### Prerekvizity

* Registracia aplikacie, nastavení *service principal*, tj. [přiřazení role aplikaci](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role), [získání *secret key*](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret). 
*App ID* a *Secret key* jsou požadovány pro nasazení aplikace( tlačítkem výše).

#### Troubleshooting

##### Podporovana verzia kubernetes

Provisioning moze skoncit nasledujucou chybou: 'The value of parameter orchestratorProfile.OrchestratorVersion is invalid'. Vtedy je nutne pozriet sa na podporovane verzie kubernetes v regionu:

```
az aks get-versions --location westeurope --output table
```

A podporovanu verziu zadat do formularu. Podporovane verzie sa casom menia..:(

### Manualne vytvorenie clusteru

#### Prerekvizity

Treba mat nainstalovane azure CLI - prikaz `az` a balicek/prikaz `jq`.
Prikazy v tomto README je nutne spustat z korenoveho adresaru tohoto repozitaru.
Pred pokracovanim v dalsich prikazoch je tiez nutne sa prihlasit prikazom `az login`.

#### Konfiguracia

```
vim scripts/config # Nastavenie nazvov rg, clusteru, registry. Pripadne nechat to co tam je, ale cluster by nemal existovat.
```

#### Vytvorenie clusteru

```
./scripts/aks/manage_cluster.sh create_rg
./scripts/aks/manage_cluster.sh create_cluster
./scripts/aks/manage_cluster.sh create_acr
./scripts/aks/manage_cluster.sh setup_credentials
```

#### Zmazanie clusteru

```
./scripts/aks/manage_cluster.sh delete_cluster
./scripts/aks/manage_cluster.sh delete_acr
./scripts/aks/manage_cluster.sh delete_rg
```

#### Vytvorenie Jenkinsu

```
./scripts/jenkins-bootstrap.sh
```

#### Jenkins login

Zistit verejnu adresu jenkins sluzby prikazom:

```
kubectl get svc cicd-jenkins
```

V stlpci `EXTERNAL-IP` je adresa, na ktoru je mozne sa pripojit cez web prehliadac a port 8080.

Meno/heslo do web rozhrania: `admin/admin`

#### Zmazanie komponent a jenkinsu

```
./scripts/jenkins-cleanup.sh
```
