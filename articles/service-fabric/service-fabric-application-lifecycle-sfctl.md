---
title: Správa aplikace Azure Service Fabric pomocí Azure Service Fabric CLI (sfctl)
description: Zjistěte, jak nasadit a odebrání aplikace z clusteru služby Azure Service Fabric pomocí Azure Service Fabric CLI
services: service-fabric
author: Christina-Kang
manager: timlt
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 2e71996e22fee34b29139fdf19764c47616beb1d
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492745"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Spravovat aplikaci Azure Service Fabric pomocí Azure Service Fabric CLI (sfctl)

Zjistěte, jak vytvářet a odstraňovat aplikace, které jsou spuštěny v clusteru Azure Service Fabric.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte sadu Service Fabric CLI. Vyberte cluster Service Fabric. Další informace najdete v tématu [Začínáme se Service Fabric CLI](service-fabric-cli.md).

* Máte připravený k nasazení balíčku aplikace Service Fabric. Další informace o tom, jak vytvořit a balíček aplikace, přečtěte si informace o [aplikačním modelem Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Přehled

Pokud chcete nasadit nové aplikace, proveďte tyto kroky:

1. Nahrání balíčku aplikace do úložiště imagí Service Fabric.
2. Zřízení typu aplikace.
3. Odstranění obsahu úložiště bitové kopie.
4. Určení a vytvořit aplikaci.
5. Určení a vytvořit služby.

Postup odebrání existující aplikace, proveďte tyto kroky:

1. Odstranění aplikace.
2. Zrušit zřízení typu přidružené aplikace.

## <a name="deploy-a-new-application"></a>Nasazení nové aplikace

K nasazení nové aplikace, proveďte následující úkoly:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Nahrát nový balíček aplikace do úložiště imagí

Než vytvoříte aplikaci, nahrajte balíček aplikace do úložiště imagí Service Fabric.

Například, pokud probíhá vašeho balíčku aplikací `app_package_dir` adresáře, použijte následující příkazy k nahrání do adresáře:

```azurecli
sfctl application upload --path ~/app_package_dir
```

U velkých balíčků aplikací, můžete zadat `--show-progress` možnosti se zobrazí průběh nahrávání.

### <a name="provision-the-application-type"></a>Zřízení typu aplikace

Po dokončení nahrávání se zřiďte aplikace. Zřídit aplikace, použijte následující příkaz:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

Hodnota pro `application-type-build-path` je název adresáře, kam jste odeslali vašeho balíčku aplikací.

### <a name="delete-the-application-package"></a>Odstranit balíček aplikace

Doporučujeme odebrat balíček aplikace po úspěšné registraci aplikace.  Odstranění balíčků aplikací z úložiště image store uvolnit systémové prostředky.  Udržování balíčky nepoužívané aplikace využívá diskové úložiště a vede k problémům s výkonem aplikací. 

Pokud chcete odstranit balíček aplikace z úložiště imagí, použijte následující příkaz:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` musí být název adresáře, který jste nahráli, když jste aplikaci vytvořili.

### <a name="create-an-application-from-an-application-type"></a>Vytvoření aplikace z typu aplikace

Po zřízení aplikace použijte následující příkaz k pojmenování a vytvořit aplikaci:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` je název, který chcete použít pro instanci aplikace. Další parametry můžete získat z manifestu aplikace dříve zřízené.

Název aplikace musí začínat předponou `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Vytvoření služby pro novou aplikaci

Po vytvoření aplikace služby vytvořte z aplikace. V následujícím příkladu vytvoříme nový bezstavovou službu z našich aplikací. Služby, které můžete vytvořit z aplikace jsou definovány v manifestu služby v balíčku aplikace dříve zřízené.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Nasazení aplikace a stavu ověření

Pokud chcete ověřit, že všechno je v pořádku, použijte následující příkazy stavu:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Pokud chcete ověřit, zda je služba v pořádku, k načtení stavu služby a aplikace použijte podobné příkazy:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

V dobrém stavu služby a aplikace mají `HealthState` hodnotu `Ok`.

## <a name="remove-an-existing-application"></a>Odeberte existující aplikace

Postup odebrání aplikace, proveďte následující úkoly:

### <a name="delete-the-application"></a>Odstranit aplikaci

Pokud chcete odstranit aplikaci, použijte následující příkaz:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Zrušit zřízení typu aplikace

Po odstranění aplikace, můžete ho už nepotřebují-li zrušit zřízení typu aplikace. Zrušit zřízení typu aplikace, použijte následující příkaz:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Název typu a verzi typu, musí odpovídat názvu a verze v manifestu aplikace dříve zřízené.

## <a name="upgrade-application"></a>Upgrade aplikace

Po vytvoření vaší aplikace, můžete opakovat stejnou sadu návod ke zřízení druhý verze aplikace. Potom se upgrade aplikace Service Fabric můžete přejít na druhou verzí aplikace. Další informace najdete v dokumentaci na [upgrady aplikací Service Fabric](service-fabric-application-upgrade.md).

Pokud chcete provést upgrade, nejdříve zřídit další verze aplikace pomocí stejných příkazů jako dříve:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Je doporučeno pak k provedení sledované automatický upgrade, spusťte upgrade spuštěním následujícího příkazu:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Upgrady přepíší stávající parametry s je libovolné sadě zadán. Parametry aplikace by měly být předány jako argumenty příkaz pro upgrade, v případě potřeby. Parametry aplikace by měla být zakódován jako objekt JSON.

Pokud chcete načíst všechny dříve zadané parametry, můžete použít `sfctl application info` příkazu.

Když probíhá upgrade aplikace, stav se dá načíst pomocí `sfctl application upgrade-status` příkazu.

Nakonec, pokud je upgrade v průběhu a musí být zrušena, můžete použít `sfctl application upgrade-rollback` vrácení upgradu.

## <a name="next-steps"></a>Další postup

* [Základní informace o Service Fabric CLI](service-fabric-cli.md)
* [Začínáme se službou Service Fabric v Linuxu](service-fabric-get-started-linux.md)
* [Spouští se upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)
