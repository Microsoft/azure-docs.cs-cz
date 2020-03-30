---
title: Správa aplikací Azure Service Fabric pomocí sfctl
description: Zjistěte, jak nasadit a odebrat aplikace z clusteru Azure Service Fabric pomocí příkazového příkazu Azure Service Fabric
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259068"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Správa aplikace Azure Service Fabric pomocí Azure Service Fabric CLI (sfctl)

Zjistěte, jak vytvářet a odstraňovat aplikace spuštěné v clusteru Azure Service Fabric.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte cli služby fabric. Potom vyberte cluster Service Fabric. Další informace naleznete [v tématu Začínáme s linkou CLI služby Fabric](service-fabric-cli.md).

* Připravte si balíček aplikace Service Fabric k nasazení. Další informace o tom, jak vytvořit a zabalit aplikaci, načtěte o [aplikačním modelu Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Přehled

Chcete-li nasadit novou aplikaci, proveďte tyto kroky:

1. Nahrajte balíček aplikace do úložiště obrázků Service Fabric.
2. Zřízení typu aplikace.
3. Odstraňte obsah úložiště obrázků.
4. Zadejte a vytvořte aplikaci.
5. Zadejte a vytvořte služby.

Chcete-li odebrat existující aplikaci, proveďte tyto kroky:

1. Odstraňte aplikaci.
2. Zrušení zřízení přidruženého typu aplikace.

## <a name="deploy-a-new-application"></a>Nasazení nové aplikace

Chcete-li nasadit novou aplikaci, proveďte následující úkoly:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Nahrání nového balíčku aplikace do úložiště obrázků

Před vytvořením aplikace nahrajte balíček aplikace do úložiště bitové kopie Service Fabric.

Pokud je například balíček `app_package_dir` aplikace v adresáři, nahrajte adresář pomocí následujících příkazů:

```shell
sfctl application upload --path ~/app_package_dir
```

U velkých balíčků aplikací můžete `--show-progress` určit možnost zobrazení průběhu nahrávání.

### <a name="provision-the-application-type"></a>Zřízení typu aplikace

Po dokončení nahrávání zřídit aplikaci. Chcete-li zřídit aplikaci, použijte následující příkaz:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

Hodnota pro `application-type-build-path` je název adresáře, do kterého jste nahráli balíček aplikace.

### <a name="delete-the-application-package"></a>Odstranění balíčku aplikace

Doporučujeme odebrat balíček aplikace po úspěšné registraci aplikace.  Odstraněním balíčků aplikací z úložiště bitové kopie uvolníte systémové prostředky.  Udržování nepoužívaných balíčků aplikací spotřebovává diskové úložiště a vede k problémům s výkonem aplikace. 

Chcete-li odstranit balíček aplikace z úložiště obrázků, použijte následující příkaz:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path`musí být název adresáře, který jste nahráli při vytváření aplikace.

### <a name="create-an-application-from-an-application-type"></a>Vytvoření aplikace z typu aplikace

Po zřízení aplikace, použijte následující příkaz k názvu a vytvoření aplikace:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name`je název, který chcete použít pro instanci aplikace. Můžete získat další parametry z dříve zřízeného manifestu aplikace.

Název aplikace musí začínat `fabric:/`předponou .

### <a name="create-services-for-the-new-application"></a>Vytvořit služby pro novou aplikaci

Po vytvoření aplikace vytvořte služby z aplikace. V následujícím příkladu vytvoříme novou bezstavovou službu z naší aplikace. Služby, které můžete vytvořit z aplikace jsou definovány v manifestu služby v dříve zřízené matné aplikace balíčku.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Ověření nasazení a stavu aplikací

Chcete-li ověřit, že je vše v pořádku, použijte následující příkazy stavu:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Chcete-li ověřit, zda je služba v pořádku, použijte podobné příkazy k načtení stavu služby i aplikace:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Zdravé služby a `HealthState` aplikace `Ok`mají hodnotu .

## <a name="remove-an-existing-application"></a>Odebrání existující aplikace

Chcete-li aplikaci odebrat, proveďte následující úkoly:

### <a name="delete-the-application"></a>Odstranit aplikaci

Chcete-li aplikaci odstranit, použijte následující příkaz:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Zrušení zřízení typu aplikace

Po odstranění aplikace můžete zrušit zřízení typu aplikace, pokud jej již nepotřebujete. Chcete-li zrušit zřízení typu aplikace, použijte následující příkaz:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Název typu a verze typu se musí shodovat s názvem a verzí v dříve zřízeném manifestu aplikace.

## <a name="upgrade-application"></a>Upgrade aplikace

Po vytvoření aplikace můžete opakovat stejnou sadu kroků pro zřízení druhé verze aplikace. Potom s upgradem aplikace Service Fabric můžete přejít na spuštění druhé verze aplikace. Další informace naleznete v dokumentaci k [upgradům aplikací Service Fabric](service-fabric-application-upgrade.md).

Chcete-li provést upgrade, nejprve zřídit další verzi aplikace pomocí stejných příkazů jako dříve:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Doporučujeme provést monitorovaný automatický upgrade, spustit upgrade spuštěním následujícího příkazu:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Upgrady přepsat existující parametry s bez ohledu na sadu je zadán. Parametry aplikace by měly být v případě potřeby předány jako argumenty příkazu upgrade. Parametry aplikace by měly být kódovány jako objekt JSON.

Chcete-li načíst všechny dříve zadané `sfctl application info` parametry, můžete použít příkaz.

Když probíhá upgrade aplikace, lze stav načíst `sfctl application upgrade-status` pomocí příkazu.

Nakonec pokud probíhá upgrade a je třeba jej zrušit, `sfctl application upgrade-rollback` můžete upgrade vrátit zpět.

## <a name="next-steps"></a>Další kroky

* [Základy příkazových příkazových odpočtek service fabric](service-fabric-cli.md)
* [Začínáme s Service Fabric na Linuxu](service-fabric-get-started-linux.md)
* [Spuštění upgradu aplikace Service Fabric](service-fabric-application-upgrade.md)
