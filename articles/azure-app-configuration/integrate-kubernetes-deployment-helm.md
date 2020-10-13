---
title: Integrace konfigurace aplikace Azure s nasazením Kubernetes pomocí Helm
description: Naučte se používat dynamické konfigurace v nasazení Kubernetes s Helm.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 2b5440ad2bec94d4ef14fa29e723cc91a4fcdf10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766861"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integrace s nasazením Kubernetes pomocí Helm

Helm poskytuje způsob, jak definovat, instalovat a upgradovat aplikace běžící v Kubernetes. Graf Helm obsahuje informace potřebné k vytvoření instance aplikace Kubernetes. Konfigurace je uložena mimo samotný graf, v souboru s názvem *Values. yaml*. 

Během procesu vydávání Helm sloučí graf se správnou konfigurací pro spuštění aplikace. Například proměnné definované v *hodnotách. yaml* mohou být odkazovány jako proměnné prostředí uvnitř spuštěných kontejnerů. Helm také podporuje vytváření tajných klíčů Kubernetes, které se dají připojit jako datové svazky nebo zveřejnit jako proměnné prostředí.

Hodnoty uložené v *hodnotách. yaml* můžete přepsat tak, že na příkazovém řádku zadáte další konfigurační soubory založené na YAML při spuštění Helm. Konfigurace aplikace Azure podporuje export hodnot konfigurace do souborů YAML. Integrací této možnosti exportu do nasazení umožníte aplikacím Kubernetes využívat konfigurační hodnoty uložené v konfiguraci aplikace.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Při nasazování aplikace do Kubernetes pomocí Helm používejte hodnoty z konfigurace aplikace.
> * Vytvoření tajného klíče Kubernetes na základě odkazu na Key Vault v konfiguraci aplikace.

V tomto kurzu se předpokládá základní znalost správy Kubernetes pomocí Helm. Přečtěte si další informace o instalaci aplikací pomocí Helm ve [službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/kubernetes-helm).

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Instalace rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (verze 2.4.0 nebo novější)
- Nainstalovat [Helm](https://helm.sh/docs/intro/install/) (verze 2.14.0 nebo novější)
- Cluster Kubernetes.

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Vyberte **Průzkumník konfigurace**  >  **vytvořit** a přidejte následující páry klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | Settings. Color | White |
    | nastavení. zpráva | Data z konfigurace aplikace Azure |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Přidat odkaz Key Vault do konfigurace aplikace
1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přidejte tajný klíč do [Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) s názvem **heslo** a **hodnotou.** 
2. Vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v předchozí části.

3. Vyberte **Průzkumník konfigurace**.

4. Vyberte **+ vytvořit**  >  **odkaz na Trezor klíčů**a pak zadejte následující hodnoty:
    - **Klíč**: vyberte **tajné klíče. Password**.
    - **Popisek**: Nechte tuto hodnotu prázdnou.
    - **Předplatné**, **Skupina prostředků**a **Trezor klíčů**: zadejte hodnoty odpovídající klíčům v trezoru klíčů, který jste vytvořili v předchozím kroku.
    - **Tajný kód**: vyberte tajný kód s názvem **heslo** , které jste vytvořili v předchozí části.

## <a name="create-helm-chart"></a>Vytvořit graf Helm ##
Nejdřív vytvořte ukázkový graf Helm pomocí následujícího příkazu.
```console
helm create mychart
```

Helm vytvoří nový adresář s názvem Mychart se strukturou uvedenou níže. 

> [!TIP]
> Další informace najdete v tomto [Průvodci grafy](https://helm.sh/docs/chart_template_guide/getting_started/) .

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Dále aktualizujte část **Specifikace: Šablona: specifikace: Containers** souboru *Deployment. yaml* . Následující fragment kódu přidá do kontejneru dvě proměnné prostředí. Hodnoty se nastavují dynamicky v době nasazení.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

Úplný soubor *Deployment. yaml* po aktualizaci by měl vypadat níže.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Pokud chcete uložit citlivá data jako Kubernetes tajné klíče, přidejte do složky Templates soubor *tajných kódů. yaml* .

> [!TIP]
> Přečtěte si další informace o používání [Kubernetes tajných klíčů](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Nakonec aktualizujte soubor *Values. yaml* následujícím obsahem, abyste mohli volitelně zadat výchozí hodnoty nastavení konfigurace a tajné klíče, na které se odkazuje v souborech *Deployment. yaml* a tajné soubory. *YAML* . Jejich skutečné hodnoty budou přepsány konfigurací získanými z konfigurace aplikace.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Předání konfigurace z konfigurace aplikace v instalaci Helm ##
Nejdřív Stáhněte konfiguraci z konfigurace aplikace do souboru *myConfig. yaml* . Pomocí klíčového filtru Stáhněte pouze ty klíče, které začínají na **nastavení.**. Pokud v takovém případě filtr klíčů není dostačující pro vyloučení klíčů Key Vault odkazů, můžete k jejich vyloučení použít argument **--Skip-a trezor** . 

> [!TIP]
> Přečtěte si další informace o [příkazu Export](https://docs.microsoft.com/cli/azure/appconfig/kv?view=azure-cli-latest#az-appconfig-kv-export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Dále Stáhněte tajné klíče do souboru s názvem *mySecrets. yaml*. Argument příkazového řádku **--Resolve-úložiště** klíčů řeší odkazy na Key Vault tím, že načtou skutečné hodnoty v Key Vault. Tento příkaz budete muset spustit s přihlašovacími údaji, které mají přístupová oprávnění k odpovídajícímu Key Vault.

> [!WARNING]
> Vzhledem k tomu, že tento soubor obsahuje citlivé informace, ponechte soubor na starosti a vyčistěte ho, když ho ještě nepotřebujete.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Použijte argument **-f** upgradu Helm a předejte dva konfigurační soubory, které jste vytvořili. Přepisují hodnoty konfigurace definované v *hodnotách. yaml* s hodnotami exportovanými z konfigurace aplikace.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

Můžete také použít argument **--set** pro upgrade Helm k předání hodnot literálních klíčů. Použití argumentu **--set** je dobrým způsobem, jak zabránit zachování citlivých dat na disk. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

Přístup k [řídicímu panelu Kubernetes](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)ověřte, že se konfigurace a tajné kódy úspěšně nastavily. Uvidíte, že **barvy** a hodnoty **zpráv** z konfigurace aplikace byly naplněny do proměnných prostředí kontejneru.

![Spuštění aplikace pro rychlý Start – místní](./media/kubernetes-dashboard-env-variables.png)

V konfiguraci aplikace se do Kubernetes tajných klíčů Přidal také jeden tajný kód a **heslo**, které obsahuje odkaz na Key Vault. 

![Spuštění aplikace pro rychlý Start – místní](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste exportovali data konfigurace aplikace Azure, která se mají používat v nasazení Kubernetes s Helm. Další informace o tom, jak používat konfiguraci aplikací, najdete v ukázkách Azure CLI.

> [!div class="nextstepaction"]
> [Azure CLI](https://docs.microsoft.com/cli/azure/appconfig?view=azure-cli-latest)
