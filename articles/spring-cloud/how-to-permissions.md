---
title: Jak používat oprávnění v Azure jaře cloudu
description: V tomto článku se dozvíte, jak vytvořit vlastní role pro oprávnění v Azure jarním cloudu.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877905"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Jak používat oprávnění v Azure jaře cloudu
V tomto článku se dozvíte, jak vytvořit vlastní role, které budou delegovat oprávnění k prostředkům služby Azure jaře Cloud. Vlastní role rozšířily [předdefinované role Azure](../role-based-access-control/built-in-roles.md) s různými oprávněními.

Implementují se tyto vlastní role:

* **Role vývojáře**: 
    * Nasadit
    * Test
    * Restartovat aplikace
    * Může použít a provést změny v konfiguracích aplikace v úložišti Git.
    * Může získat datový proud protokolu.
* Operations **-site – analýza spolehlivosti**: 
    * Restartovat aplikace
    * Získat streamy protokolů
    * Nejde provádět změny aplikací a konfigurací.
* **Role akcí Azure Pipelines/Jenkinse/GitHub**:
    * Může provádět operace vytvoření, čtení, aktualizace a odstranění.
    * Může vytvořit a nakonfigurovat všechno v Azure jaře cloudu a aplikacích v rámci instance služby: akce Azure Pipelines, Jenkinse nebo GitHubu pomocí šablon Terraformu nebo ARM.

## <a name="define-developer-role"></a>Definovat roli vývojáře

Role vývojáře zahrnuje oprávnění k restartování aplikací a zobrazení datových proudů protokolů, ale nemůže provádět změny aplikací a konfigurací.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>Navigace v předplatném a skupině prostředků řízení přístupu (IAM)

Pomocí těchto kroků můžete začít s definováním role.

1. V Azure Portal otevřete předplatné a skupinu prostředků, ve které chcete přiřadit vlastní roli.
2. Otevřete **řízení přístupu (IAM)**.
3. Klikněte na **+ Přidat**.
4. Klikněte na **Přidat vlastní roli**.
5. Klikněte na **Next** (Další).

   ![Vytvoření vlastní role](media/spring-cloud-permissions/create-custom-role.png)

6. Klikněte na tlačítko **Přidat oprávnění**.

   ![Začátek přidávání oprávnění](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Vyhledat oprávnění pro jarní Cloud v Azure:
7. Do vyhledávacího pole vyhledejte *Microsoft.app*.
Vyberte *Microsoft Azure jarní Cloud*.

   ![Vybrat jarní cloud Azure](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Vyberte oprávnění pro roli vývojáře:

Z **Microsoft. AppPlatform/pružiny** vyberte:
* Zápis: vytvoření nebo aktualizace instance služby jarní cloudová služba Azure
* Přečtěte si: získání instance služby Azure jaře Cloud Service
* Další: seznam testovacích klíčů instance služby jarní cloudová služba Azure

Z **Microsoft. AppPlatform/pružiny/aplikace** vyberte:
* Číst: čtení Microsoft Azure jarní cloudová aplikace
* Další: adresa URL pro nahrání prostředku pro zdroj cloudové aplikace Microsoft Azure

Z **Microsoft. AppPlatform/pružiny/Apps/Bindings** vyberte:
* Přečíst: čtení Microsoft Azure pružiny cloudové aplikace

Z **Microsoft. AppPlatform/pružiny/aplikace/nasazení** vyberte:
* Zápis: zápis Microsoft Azure nasazování cloudových aplikací
* Číst: čtení Microsoft Azure pružiny nasazení cloudové aplikace
* Další: spuštění Microsoft Azure jarní nasazení cloudové aplikace
* Další: zastavení Microsoft Azure jarní nasazení cloudové aplikace
* Další: restartování Microsoft Azure jarní nasazení cloudové aplikace
* Jiné: adresa URL souboru protokolu nasazení cloudové aplikace Microsoft Azure pružiny

V **Microsoft. AppPlatform/jaře/Apps/doménách** vyberte:
* Číst: čtení Microsoft Azure jarní cloudová aplikace vlastní doména

Z **Microsoft. AppPlatform/pružiny/certifikátů** vyberte:
* Přečíst: číst Microsoft Azure jarní cloudový certifikát

Z **Microsoft. AppPlatform/Locations/operationResults/pružiny** vyberte:
* Číst: výsledek operace čtení

Z **Microsoft. AppPlatform/Locations/stav operationstatus/operationId** vyberte:
* Číst: stav operace čtení

    [![Vytváření Developler oprávnění ](media/spring-cloud-permissions/developer-permissions-box.png)](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Klikněte na **Přidat**.

10. Zkontrolujte oprávnění.

11. Klikněte na **Zkontrolovat a vytvořit**.

## <a name="define-devops-engineer-role"></a>Definování role DevOps inženýr
Tento postup definuje roli s oprávněními k nasazení, testování a restartování aplikací pro jarní cloudy Azure.

1. Opakujte postup pro navigaci v předplatném, skupině prostředků a řízení přístupu (IAM).
2. Vyberte oprávnění pro roli DevOps inženýr:

Z **Microsoft. AppPlatform/pružiny** vyberte:
* Zápis: vytvoření nebo aktualizace instance služby jarní cloudová služba Azure
* Odstranit: odstranění instance služby Azure jaře Cloud Service
* Přečtěte si: získání instance služby Azure jaře Cloud Service
* Další: povolení testovacího koncového bodu instance služby jarní cloudová služba Azure
* Další: zakázat koncový bod testovacího cloudu instance služby Azure pružiny
* Další: seznam testovacích klíčů instance služby jarní cloudová služba Azure
* Další: znovu vygenerujte testovací klíč instance cloudové služby Azure jaře

Z **Microsoft. AppPlatform/pružiny/aplikace** vyberte:
* Zápis: zápis Microsoft Azure jarní cloudová aplikace
* Odstranit: odstranění Microsoft Azurech jarních cloudových aplikací
* Číst: čtení Microsoft Azure jarní cloudová aplikace
* Další: adresa URL pro nahrání prostředku pro zdroj cloudové aplikace Microsoft Azure
* Další: ověření Microsoft Azure jarní cloudová aplikace vlastní doména

Z **Microsoft. AppPlatform/pružiny/Apps/Bindings** vyberte:
* Zápis: zápis Microsoft Azure pružiny cloudové aplikace
* Odstranit: odstranění vazby cloudové aplikace Microsoft Azure pružiny
* Přečíst: čtení Microsoft Azure pružiny cloudové aplikace

Z **Microsoft. AppPlatform/pružiny/aplikace/nasazení** vyberte:
* Zápis: zápis Microsoft Azure nasazování cloudových aplikací
* Odstranit: odstranění nasazení cloudové aplikace Azure ve jarním prostředí
* Číst: čtení Microsoft Azure pružiny nasazení cloudové aplikace
* Další: spuštění Microsoft Azure jarní nasazení cloudové aplikace
* Další: zastavení Microsoft Azure jarní nasazení cloudové aplikace
* Další: restartování Microsoft Azure jarní nasazení cloudové aplikace
* Jiné: adresa URL souboru protokolu nasazení cloudové aplikace Microsoft Azure pružiny

Z **Microsoft. AppPlatform/pružiny/aplikací/nasazení/SKU** vyberte:
* Přečtěte si: seznam dostupných skladových položek nasazení aplikace

Z **umístění Microsoft. AppPlatform/Locations** vyberte:
* Jiné: Ověřte dostupnost názvu

Z Microsoft. AppPlatform/Locations/operationResults/pružiny SELECT: číst: výsledek operace čtení

Z **Microsoft. AppPlatform/Locations/stav operationstatus/operationId** vyberte:
* Číst: stav operace čtení

Z **Microsoft. AppPlatform/SKU** vyberte:
* Číst: výpis dostupných SKU

   [![Oprávnění ](media/spring-cloud-permissions/dev-ops-permissions.png) pro vývoj/op](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Klikněte na **Přidat**.

4. Zkontrolujte oprávnění.

5. Klikněte na **Zkontrolovat a vytvořit**.

## <a name="define-ops---site-reliability-engineering-role"></a>Definování role OPS-site spolehlivost
Tento postup definuje roli s oprávněními k nasazení, testování a restartování aplikací pro jarní cloudy Azure.

1. Opakujte postup pro navigaci v předplatném, skupině prostředků a řízení přístupu (IAM).

2. Vyberte oprávnění pro roli OPS-site spolehlivost:

Z **Microsoft. AppPlatform/pružiny** vyberte:
* Přečtěte si: získání instance služby Azure jaře Cloud Service
* Další: seznam testovacích klíčů instance služby jarní cloudová služba Azure

Z **Microsoft. AppPlatform/pružiny/aplikace** vyberte:
* Číst: čtení Microsoft Azure jarní cloudová aplikace

V **Microsoft. AppPlatform/Apps/nasazeních** vyberte:
* Číst: čtení Microsoft Azure pružiny nasazení cloudové aplikace
* Další: spuštění Microsoft Azure jarní nasazení cloudové aplikace
* Další: zastavení Microsoft Azure jarní nasazení cloudové aplikace
* Další: restartování Microsoft Azure jarní nasazení cloudové aplikace

Z **Microsoft. AppPlatform/Locations/operationResults/pružiny** vyberte:
* Číst: výsledek operace čtení

Z **Microsoft. AppPlatform/Locations/stav operationstatus/operationId** vyberte:
* Číst: stav operace čtení

   [![Oprávnění ](media/spring-cloud-permissions/ops-sre-permissions.png) OPS/SRE](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Klikněte na **Přidat**.

4. Zkontrolujte oprávnění.

5. Klikněte na **Zkontrolovat a vytvořit**.

## <a name="define-azure-pipelinesprovisioning-role"></a>Definování role Azure Pipelines/zřizování
Tato role akcí Jenkinse/GitHubu může vytvářet a konfigurovat vše v Azure jaře cloudu a aplikacích s instancí služby. Tato role je určena pro uvolnění nebo nasazení kódu.

1. Opakujte postup pro navigaci v předplatném, skupině prostředků a řízení přístupu (IAM).

2. Otevřete možnosti **oprávnění** .

3. Vyberte oprávnění pro roli Azure Pipelines/zřizování:
  
Z **Microsoft. AppPlatform/pružiny** vyberte:
* Zápis: vytvoření nebo aktualizace instance služby jarní cloudová služba Azure
* Odstranit: odstranění instance služby Azure jaře Cloud Service
* Přečtěte si: získání instance služby Azure jaře Cloud Service
* Další: povolení testovacího koncového bodu instance služby jarní cloudová služba Azure
* Další: zakázat koncový bod testovacího cloudu instance služby Azure pružiny
* Další: seznam testovacích klíčů instance služby jarní cloudová služba Azure
* Další: znovu vygenerujte testovací klíč instance cloudové služby Azure jaře

Z **Microsoft. AppPlatform/pružiny/aplikace** vyberte:
* Zápis: zápis Microsoft Azure jarní cloudová aplikace
* Odstranit: odstranění Microsoft Azurech jarních cloudových aplikací
* Číst: čtení Microsoft Azure jarní cloudová aplikace
* Další: adresa URL pro nahrání prostředku pro zdroj cloudové aplikace Microsoft Azure
* Další: ověření Microsoft Azure jarní cloudová aplikace vlastní doména

Z **Microsoft. AppPlatform/pružiny/Apps/Bindings** vyberte:
* Zápis: zápis Microsoft Azure pružiny cloudové aplikace
* Odstranit: odstranění vazby cloudové aplikace Microsoft Azure pružiny
* Přečíst: čtení Microsoft Azure pružiny cloudové aplikace

Z **Microsoft. AppPlatform/pružiny/aplikace/nasazení** vyberte:
* Zápis: zápis Microsoft Azure nasazování cloudových aplikací
* Odstranit: odstranění nasazení cloudové aplikace Azure ve jarním prostředí
* Číst: čtení Microsoft Azure pružiny nasazení cloudové aplikace
* Další: spuštění Microsoft Azure jarní nasazení cloudové aplikace
* Další: zastavení Microsoft Azure jarní nasazení cloudové aplikace
* Další: restartování Microsoft Azure jarní nasazení cloudové aplikace
* Jiné: adresa URL souboru protokolu nasazení cloudové aplikace Microsoft Azure pružiny

Z **Microsoft. AppPlatform/SKU** vyberte:
* Číst: výpis dostupných SKU

Z **umístění Microsoft. AppPlatform/Locations** vyberte:
* Jiné: Ověřte dostupnost názvu

Z **Microsoft. AppPlatform/Locations/operationResults/pružiny** vyberte:
* Číst: výsledek operace čtení

Z **Microsoft. AppPlatform/Locations/stav operationstatus/operationId** vyberte:
* Číst: stav operace čtení

Z **Microsoft. AppPlatform/SKU** vyberte:
* Číst: výpis dostupných SKU

   [![Oprávnění ](media/spring-cloud-permissions/pipelines-permissions-box.png) kanálu](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. Klikněte na **Přidat**.

5. Zkontrolujte oprávnění.

6. Klikněte na **Zkontrolovat a vytvořit**.


## <a name="see-also"></a>Viz také
* [Vytvoření nebo aktualizace vlastních rolí pomocí portálu Azure Portal](../role-based-access-control/custom-roles-portal.md)

Další informace o třech metodách, které definují vlastní oprávnění, najdete v těchto tématech:
* [Naklonování role](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [Začátek od nuly](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [Začít od formátu JSON](../role-based-access-control/custom-roles-portal.md#start-from-json)