---
title: Spravovat aplikace Azure Service Fabric pomocí Azure Service Fabric rozhraní příkazového řádku (sfctl)
description: Zjistěte, jak nasadit a odebrání aplikace z clusteru služby Azure Service Fabric pomocí příkazového řádku Azure Service Fabric
services: service-fabric
author: Christina-Kang
manager: timlt
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: bikang
ms.openlocfilehash: 2cbc5778385a5a4af3f6dc0306e2b943482bf40c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642882"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Spravovat aplikace Azure Service Fabric pomocí Azure Service Fabric rozhraní příkazového řádku (sfctl)

Naučte se vytvářet a odstraňovat aplikace, které jsou spuštěny v clusteru služby Azure Service Fabric.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte Service Fabric rozhraní příkazového řádku. Pak vyberte cluster Service Fabric. Další informace najdete v tématu [začít pracovat s Service Fabric rozhraní příkazového řádku](service-fabric-cli.md).

* Máte připravený k nasazení balíčku aplikace Service Fabric. Další informace o tom, jak vytvořit a balíček aplikace, přečtěte si informace o [model aplikace Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Přehled

Pokud chcete nasadit novou aplikaci, proveďte tyto kroky:

1. Nahrajte balíček aplikace do úložiště bitové kopie Service Fabric.
2. Zřídit typ aplikace.
3. Odstranění obsahu úložiště bitové kopie.
4. Zadejte a vytvoření aplikace.
5. Zadejte a vytvoření služeb.

Odebrat existující aplikaci, proveďte tyto kroky:

1. Odstraňte aplikaci.
2. Zrušit zřízení typu přidružené aplikaci.

## <a name="deploy-a-new-application"></a>Nasazení nové aplikace

Pokud chcete nasadit novou aplikaci, proveďte následující úlohy:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Nahrát nový balíček aplikace do úložiště bitové kopie

Než vytvoříte aplikaci, nahrajte balíček aplikace do úložiště bitové kopie Service Fabric.

Například, pokud probíhá balíčku aplikace `app_package_dir` adresáře, použijte následující příkazy pro nahrání adresáři:

```azurecli
sfctl application upload --path ~/app_package_dir
```

U velkých balíčků aplikací, můžete zadat `--show-progress` možnosti zobrazíte průběh nahrávání.

### <a name="provision-the-application-type"></a>Zřízení typu aplikace

Po dokončení nahrávání se zřídit aplikace. Zřídit aplikace, použijte následující příkaz:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

Hodnota `application-type-build-path` je název adresáře, kde jste nahráli balíčku aplikace.

### <a name="delete-the-application-package"></a>Odstranění balíčku aplikace

Doporučujeme odebrat balíček aplikace, po úspěšném zaregistrování aplikace.  Odstranění balíčky aplikací z úložiště image store uvolnit systémové prostředky.  Udržování balíčků nepoužívané aplikace využívá diskového úložiště a vede k problémům s výkonem aplikace. 

K odstranění balíčku aplikace z úložiště bitových kopií, použijte následující příkaz:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` musí být název adresáře, který jste nahráli při vytváření aplikace.

### <a name="create-an-application-from-an-application-type"></a>Vytvoření aplikace z typ aplikace

Po zřízení aplikace, použijte následující příkaz k pojmenování a vytvoření aplikace:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` je název, který chcete použít pro instanci aplikace. Manifest dříve zřízené aplikace můžete získat další parametry.

Název aplikace musí začínat předponu `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Vytvoření nové aplikace služby

Po vytvoření aplikace, vytvořte z aplikace služby. V následujícím příkladu vytvoříme nové bezstavové služby z naší aplikace. Služby, které můžete vytvořit z aplikace jsou definovány v service manifest v balíčku dříve zřízené aplikace.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Ověřte stav a nasazení aplikace

Pokud chcete ověřit, že všechno, co je v pořádku, použijte následující příkazy stavu:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Pokud chcete ověřit, že služba je v pořádku, použijte příkazy podobné načíst stav služby a aplikace:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

V pořádku služeb a aplikací mít `HealthState` hodnotu `Ok`.

## <a name="remove-an-existing-application"></a>Odeberte stávající aplikaci

Chcete-li odebrat, proveďte následující úlohy:

### <a name="delete-the-application"></a>Odstranit aplikaci

Pokud chcete odstranit aplikaci, použijte následující příkaz:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Zrušit zřízení typu aplikace

Po odstranění aplikace, můžete zrušit zřízení typu aplikace již nepotřebujete. Chcete-li zrušit zřízení typu aplikace, použijte následující příkaz:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Zadejte název a typ verze musí odpovídat názvem a verzí v manifestu dříve zřízené aplikace.

## <a name="upgrade-application"></a>Upgrade aplikace

Po vytvoření aplikace, můžete opakovat stejnou sadu návod ke zřízení druhou verzi vaší aplikace. Upgradu aplikace Service Fabric potom můžete přejít na druhý verzí aplikace. Další informace najdete v dokumentaci na [upgradů aplikací Service Fabric](service-fabric-application-upgrade.md).

Pokud chcete provést upgrade, nejdříve zřídit příští verze aplikace, jako předtím pomocí stejné příkazy:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Doporučujeme pak k provedení sledované automatického upgradu, spusťte upgrade spuštěním následujícího příkazu:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Upgrady přepsat existující parametry s jakoukoli sadu je zadán. Parametry aplikačního mají být předány jako argumenty pro příkaz pro upgrade, v případě potřeby. Parametry aplikačního by měl být zakódován jako objekt JSON.

Pokud chcete načíst všechny dříve zadané parametry, můžete použít `sfctl application info` příkaz.

Když probíhá upgrade aplikace, stav se dá načíst pomocí `sfctl application upgrade-status` příkaz.

Nakonec, pokud je v průběhu a musí být zrušena upgrade, můžete použít `sfctl application upgrade-rollback` vrácení upgradu.

## <a name="next-steps"></a>Další postup

* [Základy služby Fabric rozhraní příkazového řádku](service-fabric-cli.md)
* [Začínáme se službou Service Fabric v systému Linux](service-fabric-get-started-linux.md)
* [Spuštění upgradu aplikace Service Fabric](service-fabric-application-upgrade.md)
