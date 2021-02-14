---
title: Vytvoření PostgreSQL skupiny serverů s škálovatelným škálováním pomocí nástrojů Kubernetes
description: Vytvoření PostgreSQL skupiny serverů s škálovatelným škálováním pomocí nástrojů Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 250c1ef837793c2149ff653f395f40272cf43335
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384941"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>Vytvoření PostgreSQL skupiny serverů s škálovatelným škálováním pomocí nástrojů Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Požadavky

Měli byste už mít vytvořený [řadič dat ARC Azure](./create-data-controller.md).

Pokud chcete vytvořit PostgreSQL skupinu serverů s škálovatelným škálováním pomocí nástrojů Kubernetes, budete muset mít nainstalované nástroje Kubernetes Tools.  Příklady v tomto článku budou použity `kubectl` , ale podobné přístupy lze použít s jinými Kubernetes nástroji, jako je řídicí panel Kubernetes, `oc` nebo `helm` Pokud s těmito nástroji a Kubernetes YAML/JSON znáte.

[Instalace nástroje kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Přehled

Pokud chcete vytvořit PostgreSQL skupinu serverů s škálovatelným škálováním, budete muset vytvořit Kubernetes tajný klíč, abyste mohli bezpečně uložit přihlašovací jméno a heslo správce Postgres a PostgreSQL vlastní prostředky skupiny serverů s vlastním škálováním na základě vlastních definic prostředků PostgreSQL-12 nebo PostgreSQL-11.

## <a name="create-a-yaml-file"></a>Vytvoření souboru YAML

Soubor [šablony YAML](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/postgresql.yaml) můžete použít jako výchozí bod pro vytvoření vlastního PostgreSQL souboru YAML skupiny serverů s vlastním škálováním.  Stáhněte si tento soubor do místního počítače a otevřete ho v textovém editoru.  Je užitečné použít textový editor, například [vs Code](https://code.visualstudio.com/download) , který podporuje zvýrazňování syntaxe a linting pro soubory YAML.

Toto je příklad souboru YAML:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: pg1-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1alpha1
kind: postgresql-12
metadata:
  generation: 1
  name: pg1
spec:
  engine:
    extensions:
    - name: citus
  scale:
    shards: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Přizpůsobení přihlašovacích údajů a hesla
Tajný kód Kubernetes je uložen jako řetězec kódovaný v kódování Base64 – jeden pro uživatelské jméno a jeden pro heslo.  Budete muset kódovat přihlašovací jméno správce a heslo a umístit je do zástupného umístění v `data.password` a `data.username` .  Nezahrnujte `<` `>` symboly a uvedené v šabloně.

Můžete použít online nástroj k kódování Base64 vaše požadované uživatelské jméno a heslo nebo můžete v závislosti na vaší platformě použít integrované nástroje rozhraní příkazového řádku.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

### <a name="customizing-the-name"></a>Přizpůsobení názvu

Šablona má pro atribut Name hodnotu ' SO1 '.  Můžete to změnit, ale musí to být znaky, které následují jako standard názvů DNS.  Je také nutné změnit název tajného kódu tak, aby odpovídal.  Pokud například změníte název skupiny serverů PostgreSQL s podporou škálování na SO2, musíte změnit název tajného kódu z ' SO1-Login-Secret ' na ' SO2-Login-tajná '

### <a name="customizing-the-engine-version"></a>Přizpůsobení verze modulu

Verzi modulu můžete změnit tak, aby byla buď PostgreSQL-11, nebo PostgreSQL-12 úpravou `kind` atributu.

### <a name="customizing-the-resource-requirements"></a>Přizpůsobení požadavků na prostředky

Požadavky na prostředky můžete měnit – podle potřeby paměť RAM a základní omezení a požadavky.  

> [!NOTE]
> Další informace o [správě prostředků Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)najdete tady.

Požadavky na limity a požadavky prostředků:
- Hodnota limit počtu jader se **vyžaduje** pro účely fakturace.
- Zbytek požadavků a omezení prostředků je nepovinný.
- Omezení počtu jader a požadavek musí být kladná celočíselná hodnota, je-li zadána.
- Pro požadavek na jádro se vyžaduje minimálně 1 jádro, pokud je zadané.
- Formát hodnot paměti následuje po Kubernetes zápisu.  

### <a name="customizing-service-type"></a>Přizpůsobení typu služby

Typ služby může být v případě potřeby změněn na NodePort.  Bude přiřazeno náhodné číslo portu.

### <a name="customizing-storage"></a>Přizpůsobení úložiště

Můžete přizpůsobit třídy úložiště pro úložiště tak, aby odpovídaly vašemu prostředí.  Pokud si nejste jistí, které třídy úložiště jsou k dispozici, můžete spustit příkaz `kubectl get storageclass` a zobrazit je.  Šablona má výchozí hodnotu default.  To znamená, že je k dispozici třída úložiště _s názvem_ Default, ale neexistuje třída úložiště, která _je_ výchozím nastavením.  Volitelně můžete také změnit velikost úložiště.  Můžete si přečíst další informace o [konfiguraci úložiště](./storage-configuration.md).

## <a name="creating-the-postgresql-hyperscale-server-group"></a>Vytváření PostgreSQL skupiny serverů s škálovatelným škálováním

Teď, když jste přizpůsobili soubor YAML skupiny PostgreSQL, můžete vytvořit skupinu serverů PostgreSQL s vlastním škálováním pomocí následujícího příkazu:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>Monitorování stavu vytváření

Vytvoření skupiny serverů PostgreSQL se škálováním na více systémů bude trvat několik minut. Průběh můžete sledovat v jiném okně terminálu pomocí následujících příkazů:

> [!NOTE]
>  V ukázkových příkazech se předpokládá, že jste vytvořili PostgreSQL skupinu serverů s názvem SO1 a obor názvů Kubernetes s názvem ARC.  Pokud jste použili jiný název skupiny serverů PostgreSQL s vlastním rozsahem, můžete "ARC" a "SO1" nahradit názvy.

```console
kubectl get postgresql-12/pg1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Stav vytvoření kteréhokoli z nich můžete také ověřit spuštěním příkazu podobného níže.  To je užitečné hlavně při řešení problémů.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/pg1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Řešení potíží s vytvářením

Pokud narazíte na potíže s vytvářením, přečtěte si [příručku Poradce při potížích](troubleshoot-guide.md).