---
title: Správa aplikací Service Fabric v Azure pomocí sfctl
description: Naučte se nasazovat a odebírat aplikace z clusteru Azure Service Fabric pomocí Azure Service Fabric CLI.
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84711030"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Správa aplikace Service Fabric Azure pomocí Azure Service Fabric CLI (sfctl)

Naučte se vytvářet a odstraňovat aplikace, které běží v clusteru Azure Service Fabric.

## <a name="prerequisites"></a>Předpoklady

* Nainstalujte Service Fabric CLI. Pak vyberte svůj Cluster Service Fabric. Další informace najdete v tématu [Začínáme s Service Fabric CLI](service-fabric-cli.md).

* Připravte balíček aplikace Service Fabric, který se má nasadit. Další informace o tom, jak vytvořit a zabalit aplikaci, najdete v článku o [Service Fabric aplikačním modelu](service-fabric-application-model.md).

## <a name="overview"></a>Přehled

Chcete-li nasadit novou aplikaci, proveďte tyto kroky:

1. Nahrajte balíček aplikace do úložiště imagí Service Fabric.
2. Zřídí typ aplikace.
3. Odstraňte obsah úložiště imagí.
4. Zadejte a vytvořte aplikaci.
5. Zadejte a vytvořte služby.

Chcete-li odebrat existující aplikaci, proveďte tyto kroky:

1. Odstraňte aplikaci.
2. Zrušení zřízení přidruženého typu aplikace.

## <a name="deploy-a-new-application"></a>Nasazení nové aplikace

Chcete-li nasadit novou aplikaci, proveďte následující úlohy:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Nahrání nového balíčku aplikace do úložiště imagí

Před vytvořením aplikace nahrajte balíček aplikace do úložiště imagí Service Fabric.

Například pokud je balíček aplikace v `app_package_dir` adresáři, použijte následující příkazy pro nahrání adresáře:

```shell
sfctl application upload --path ~/app_package_dir
```

Pro velké balíčky aplikací můžete zadat `--show-progress` možnost zobrazení průběhu nahrávání.

### <a name="provision-the-application-type"></a>Zřídit typ aplikace

Po dokončení nahrávání zajistěte aplikaci. Chcete-li zřídit aplikaci, použijte následující příkaz:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

Hodnota pro `application-type-build-path` je název adresáře, do kterého jste nahráli balíček aplikace.

### <a name="delete-the-application-package"></a>Odstranění balíčku aplikace

Doporučuje se odebrat balíček aplikace po úspěšné registraci aplikace.  Odstranění balíčků aplikace z úložiště imagí uvolní systémové prostředky.  Udržování nepoužívaných balíčků aplikací spotřebovává diskové úložiště a vede k problémům s výkonem aplikací. 

Chcete-li odstranit balíček aplikace z úložiště imagí, použijte následující příkaz:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path` musí se jednat o název adresáře, který jste nahráli při vytváření aplikace.

### <a name="create-an-application-from-an-application-type"></a>Vytvoření aplikace z typu aplikace

Po zřízení aplikace použijte následující příkaz k pojmenování a vytvoření aplikace:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` je název, který chcete použít pro instanci aplikace. Můžete získat další parametry z dříve zřízeného manifestu aplikace.

Název aplikace musí začínat předponou `fabric:/` .

### <a name="create-services-for-the-new-application"></a>Vytvořit služby pro novou aplikaci

Po vytvoření aplikace vytvořte služby z aplikace. V následujícím příkladu vytvoříme z naší aplikace novou bezstavovou službu. Služby, které můžete vytvořit z aplikace, jsou definovány v manifestu služby v dříve zřízeném balíčku aplikace.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Ověření nasazení aplikace a stavu

Chcete-li ověřit, zda je vše v pořádku, použijte následující příkazy pro stav:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Chcete-li ověřit, zda je služba v pořádku, použijte podobné příkazy k načtení stavu služby i aplikace:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Služby a aplikace v pořádku mají `HealthState` hodnotu `Ok` .

## <a name="remove-an-existing-application"></a>Odebrání existující aplikace

Chcete-li odebrat aplikaci, proveďte následující úlohy:

### <a name="delete-the-application"></a>Odstranění aplikace

Pokud chcete aplikaci odstranit, použijte následující příkaz:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Zrušit zřízení typu aplikace

Po odstranění aplikace můžete typ aplikace zrušit, pokud ji už nepotřebujete. Chcete-li zrušit zřízení typu aplikace, použijte následující příkaz:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Název typu a verze typu se musí shodovat s názvem a verzí v dřív zřízeném manifestu aplikace.

## <a name="upgrade-application"></a>Upgradovat aplikaci

Po vytvoření aplikace můžete zopakovat stejnou sadu kroků pro zřízení druhé verze aplikace. Pak se pomocí upgradu Service Fabric aplikace můžete převést na provoz druhé verze aplikace. Další informace najdete v dokumentaci k [Service Fabric upgrady aplikací](service-fabric-application-upgrade.md).

Chcete-li provést upgrade, nejprve zajistěte novou verzi aplikace pomocí stejných příkazů jako dříve:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Doporučuje se následně provést monitorovaný automatický upgrade, spustit upgrade spuštěním následujícího příkazu:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Upgrady přepíšou existující parametry s libovolnou nastavenou sadou. Parametry aplikace by měly být v případě potřeby předány jako argumenty příkazu upgrade. Parametry aplikace by měly být kódované jako objekt JSON.

Chcete-li načíst všechny parametry, které byly zadány dříve, můžete použít `sfctl application info` příkaz.

Když probíhá upgrade aplikace, lze stav získat pomocí `sfctl application upgrade-status` příkazu.

Nakonec, pokud probíhá upgrade a je potřeba ho zrušit, můžete `sfctl application upgrade-rollback` k obnovení upgradu použít.

## <a name="next-steps"></a>Další kroky

* [Základy rozhraní příkazového řádku Service Fabric](service-fabric-cli.md)
* [Začínáme s Service Fabric v systému Linux](service-fabric-get-started-linux.md)
* [Spouští se upgrade aplikace Service Fabric.](service-fabric-application-upgrade.md)
