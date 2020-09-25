---
title: Vytvoření spravované instance SQL pomocí nástrojů Kubernetes
description: Vytvoření spravované instance SQL pomocí nástrojů Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 80aa323dfb08ec1e8156099bd5d04e790732b3b0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342320"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Vytvoření spravované instance Azure SQL pomocí nástrojů Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Požadavky

Měli byste už mít vytvořený [řadič dat ARC Azure](./create-data-controller.md).

K vytvoření spravované instance SQL pomocí nástrojů Kubernetes budete muset mít nainstalované nástroje Kubernetes.  Příklady v tomto článku budou použity `kubectl` , ale podobné přístupy lze použít s jinými Kubernetes nástroji, jako je řídicí panel Kubernetes, `oc` nebo `helm` Pokud s těmito nástroji a Kubernetes YAML/JSON znáte.

[Instalace nástroje kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Přehled

Chcete-li vytvořit spravovanou instanci SQL, je třeba vytvořit tajný klíč Kubernetes k bezpečnému uložení přihlašovacího jména a hesla správce systému a vlastního prostředku spravované instance SQL na základě definice vlastního prostředku sqlmanagedinstance.

## <a name="create-a-yaml-file"></a>Vytvoření souboru YAML

Soubor [šablony YAML](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/sqlmi.yaml) můžete použít jako výchozí bod k vytvoření vlastního souboru YAML spravované instance SQL.  Stáhněte si tento soubor do místního počítače a otevřete ho v textovém editoru.  Je užitečné použít textový editor, například [vs Code](https://code.visualstudio.com/download) , který podporuje zvýrazňování syntaxe a linting pro soubory YAML.

Toto je příklad souboru YAML:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: example-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: example
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    datalogs:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Přizpůsobení přihlašovacích údajů a hesla

Tajný kód Kubernetes je uložen jako řetězec kódovaný v kódování Base64 – jeden pro uživatelské jméno a jeden pro heslo.  Budete potřebovat kódování Base64 pro správce systému a heslo a umístit je do zástupného umístění v `data.password` a `data.username` .  Nezahrnujte `<` `>` symboly a uvedené v šabloně.

> [!NOTE]
> Pro optimální zabezpečení není pro přihlášení povoleno použití hodnoty ' sa '.
> Postupujte podle [zásad složitosti hesel](/sql/relational-databases/security/password-policy#password-complexity).

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

Šablona má pro atribut Name hodnotu example.  Můžete to změnit, ale musí to být znaky, které následují jako standard názvů DNS.  Je také nutné změnit název tajného kódu tak, aby odpovídal.  Pokud například změníte název spravované instance SQL na ' SQL1 ', musíte změnit název tajného kódu z ' příklad-Login-tajné ' na ' SQL1-Login-Secret '

### <a name="customizing-the-resource-requirements"></a>Přizpůsobení požadavků na prostředky

Požadavky na prostředky můžete měnit – podle potřeby paměť RAM a základní omezení a požadavky.  

> [!NOTE]
> Další informace o [správě prostředků Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)najdete tady.

Požadavky na limity a požadavky prostředků:
- Hodnota limit počtu jader se **vyžaduje** pro účely fakturace.
- Zbytek požadavků a omezení prostředků je nepovinný.
- Omezení počtu jader a požadavek musí být kladná celočíselná hodnota, je-li zadána.
- Pro požadavek na jádra se vyžaduje minimálně 2 jádra, pokud je zadaná.
- Formát hodnot paměti následuje po Kubernetes zápisu.  
- Pro požadavek na paměť, je-li zadána, je nutné zadat minimálně 2Gi.
- Obecně platí, že byste měli mít 4 GB paměti RAM pro každou 1 jádro v produkčních případech použití.

### <a name="customizing-service-type"></a>Přizpůsobení typu služby

Typ služby může být v případě potřeby změněn na NodePort.  Bude přiřazeno náhodné číslo portu.

### <a name="customizing-storage"></a>Přizpůsobení úložiště

Můžete přizpůsobit třídy úložiště pro úložiště tak, aby odpovídaly vašemu prostředí.  Pokud si nejste jistí, které třídy úložiště jsou k dispozici, můžete spustit příkaz `kubectl get storageclass` a zobrazit je.  Šablona má výchozí hodnotu default.  To znamená, že je k dispozici třída úložiště _s názvem_ Default, ale neexistuje třída úložiště, která _je_ výchozím nastavením.  Volitelně můžete také změnit velikost úložiště.  Můžete si přečíst další informace o [konfiguraci úložiště](./storage-configuration.md).

## <a name="creating-the-sql-managed-instance"></a>Vytváření spravované instance SQL

Teď, když jste přizpůsobili soubor YAML Managed instance SQL, můžete vytvořit spravovanou instanci SQL spuštěním následujícího příkazu:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>Monitorování stavu vytváření

Vytvoření spravované instance SQL zabere několik minut, než se dokončí. Průběh můžete sledovat v jiném okně terminálu pomocí následujících příkazů:

> [!NOTE]
>  V ukázkových příkazech se předpokládá, že jste vytvořili spravovanou instanci SQL nazvanou ' SQL1 ' a obor názvů Kubernetes s názvem ' ARC '.  Pokud jste použili jiný obor názvů nebo název spravované instance SQL, můžete "ARC" a "sqlmi" nahradit názvy.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Stav vytvoření kteréhokoli z nich můžete také ověřit spuštěním příkazu podobného níže.  To je užitečné hlavně při řešení problémů.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Řešení potíží s vytvářením

Pokud narazíte na potíže s vytvářením, přečtěte si [příručku Poradce při potížích](troubleshoot-guide.md).

## <a name="next-steps"></a>Další kroky

[Připojení ke spravované instanci SQL ARC s povoleným voláním Azure](connect-managed-instance.md)
