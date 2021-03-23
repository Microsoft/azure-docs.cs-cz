---
title: Aplikace a nasazení v Azure jaře cloudu
description: Toto téma vysvětluje rozdíl mezi aplikací a nasazením v Azure jaře cloudu.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5473daedc8a7ad5a3b6ddffc65234160d4b3019d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878150"
---
# <a name="app-and-deployment-in-azure-spring-cloud"></a>Aplikace a nasazení v Azure jaře cloudu

**Tento článek se týká:** ✔️ Java ✔️ C #

**Aplikace** a **nasazení** jsou dvě klíčové koncepty v modelu prostředků Azure jarního cloudu. V Azure jaře Cloud je *aplikace* abstrakcí jedné obchodní aplikace nebo jedné mikroslužby.  Jedna verze kódu nebo binárního souboru nasazeného jako *aplikace* běží v *nasazení*.  Aplikace běží v *instanci služby jarní cloudová služba Azure* nebo jednoduše *instance služby*, jak je uvedeno dále.

 ![Aplikace a nasazení](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

V rámci jednoho předplatného Azure můžete mít několik instancí služby, ale pokud jsou všechny aplikace, které tvoří podnikovou aplikaci nebo mikroslužby, umístěné v rámci jedné instance služby, je nejjednodušší použít tuto službu jarní cloudová služba Azure.

Standardní úroveň cloudu Azure umožňuje, aby jedna aplikace měla jedno výrobní nasazení a jedno pracovní nasazení, takže můžete snadno provádět Blue/zelená nasazení.

## <a name="app"></a>Aplikace
Na úrovni aplikace jsou definované následující funkce nebo vlastnosti.

| Funkce | Popis |
|:--|:----------------|
| Veřejná</br>Koncový bod | Adresa URL pro přístup k aplikaci |
| Vlastní</br>Doména | Záznam CNAME, který zabezpečuje vlastní doménu |
| Služba</br>Vazba | Připojení k jiným službám Azure mimo box |
| Spravované</br>Identita | Spravovaná identita pomocí Azure Active Directory umožňuje vaší aplikaci snadný přístup k dalším prostředkům chráněným službou Azure AD, jako je například Azure Key Vault |
| Trvalý</br>Storage | Nastavení, které umožňuje zachovat data po restartování aplikace |

## <a name="deployment"></a>Nasazení

Následující funkce nebo vlastnosti jsou definovány na úrovni nasazení a budou vyměněny při záměně produkčního a přípravného nasazení.

| Funkce | Popis |
|:--|:----------------|
| Procesor | Počet virtuální jádra na instanci aplikace |
| Memory (Paměť) | GB paměti na instanci aplikace|
| Instance</br>Počet | Počet instancí aplikace, nastavit ručně nebo automaticky |
| Automatické škálování | Automatické škálování počtu instancí na základě předdefinovaných pravidel a plánů |
| JVM</br>Možnosti | Nastavení možností JVM  |
| Prostředí</br>Proměnné | Nastavení proměnných prostředí |
| Runtime (Modul runtime)</br>Verze | Java 8/Java 11|

## <a name="restrictions"></a>Omezení

* **Aplikace musí mít jedno nasazení v produkčním** prostředí: odstranění produkčního nasazení je BLOKOVÁNo rozhraním API. Měl by být před odstraněním záměna na fázování.
* **Aplikace může mít maximálně dvě nasazení**: vytvoření více než dvou nasazení je BLOKOVÁNo rozhraním API. Nasaďte nový binární soubor do stávajícího produkčního nebo pracovního nasazení.
* **Správa nasazení není na úrovni Basic k dispozici**: pro Blue-Green možnosti nasazení použijte úroveň Standard.

## <a name="see-also"></a>Viz také
* [Nastavení přípravného prostředí v Azure jaře cloudu](spring-cloud-howto-staging-environment.md)
