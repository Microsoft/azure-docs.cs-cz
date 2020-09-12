---
title: Pochopení aplikace a nasazení v Azure jaře cloudu
description: Rozdíl mezi aplikací a nasazením v Azure jaře cloudu.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: a1f6d318c123b5907a8c434bb097fb86a351f5d1
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297531"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Pochopení aplikace a nasazení v Azure jaře cloudu

**Aplikace** a **nasazení** jsou dvě klíčové koncepty v modelu prostředků Azure jarního cloudu. V Azure jaře Cloud je *aplikace* abstrakcí jedné obchodní aplikace nebo jedné mikroslužby.  Jedna verze kódu nebo binárního souboru nasazeného jako *aplikace* běží v *nasazení*.  Aplikace běží v *instanci služby jarní cloudová služba Azure*nebo jednoduše *instance služby*, jak je uvedeno dále.

 ![Aplikace a nasazení](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

V rámci jednoho předplatného Azure můžete mít několik instancí služby, ale pokud jsou všechny aplikace, které tvoří podnikovou aplikaci nebo mikroslužby, umístěné v rámci jedné instance služby, je nejjednodušší použít tuto službu jarní cloudová služba Azure.

Standardní úroveň cloudu Azure umožňuje, aby jedna aplikace měla jedno výrobní nasazení a jedno pracovní nasazení, takže můžete snadno provádět Blue/zelená nasazení.

## <a name="app"></a>Aplikace
Na úrovni aplikace jsou definované následující funkce nebo vlastnosti.

| Výčet | Definice |
|:--|:----------------|
| Public</br>Koncový bod | Adresa URL pro přístup k aplikaci |
| Vlastní</br>Doména | Záznam CNAME, který zabezpečuje vlastní doménu |
| Služba</br>Vazba | Vlastnosti konfigurace vazby nastavené v function.jspro soubor a atribut *ServiceBusTrigger* |
| Spravované</br>Identita | Spravovaná identita pomocí Azure Active Directory umožňuje vaší aplikaci snadný přístup k dalším prostředkům chráněným službou Azure AD, jako je například Azure Key Vault |
| Trvalý</br>Storage | Nastavení, které umožňuje zachovat data po restartování aplikace |

## <a name="deployment"></a>Nasazení

Následující funkce nebo vlastnosti jsou definovány na úrovni nasazení a budou vyměněny při záměně produkčního a přípravného nasazení.

| Výčet | Definice |
|:--|:----------------|
| Procesor | Počet virtuální jádra na instanci aplikace |
| Paměť | Nastavení, které přiděluje paměť pro horizontální navýšení nebo navýšení kapacity nasazení |
| Instance</br>Počet | Počet instancí aplikace, nastavit ručně nebo automaticky |
| Automatické škálování | Automatické škálování počtu instancí na základě předdefinovaných pravidel a plánů |
| JVM</br>Možnosti | nastavení: JAVA_OPTS |
| Prostředí</br>Proměnné | Nastavení, která se vztahují na celé jaře cloudové prostředí Azure |
| Modul runtime</br>Verze | Java 8/Java 11|

## <a name="restrictions"></a>Omezení

* **Aplikace musí mít jedno nasazení v produkčním**prostředí: odstranění produkčního nasazení je BLOKOVÁNo rozhraním API. Měl by být před odstraněním záměna na fázování.
* **Aplikace může mít maximálně dvě nasazení**: vytvoření více než dvou nasazení je BLOKOVÁNo rozhraním API. Nasaďte nový binární soubor do stávajícího produkčního nebo pracovního nasazení.
* **Správa nasazení není na úrovni Basic k dispozici**: pro možnost nasazení Blue-zelená použijte úroveň Standard.

## <a name="see-also"></a>Viz také
* [Nastavení přípravného prostředí v Azure jaře cloudu](spring-cloud-howto-staging-environment.md)
