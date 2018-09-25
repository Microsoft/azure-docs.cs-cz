---
title: Monitorování hrozeb ve službě Azure Storage
description: Konfigurace Azure Storage Advanced Threat Protection detekovat anomálie v aktivitě účtu a upozornit vás na potenciálně nebezpečné pokusy o přístup k vašemu účtu.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 09/24/2018
ms.author: ronmat
ms.manager: shaik
ms.openlocfilehash: 8b2ca2d5d6418d68cab847df80fc437e468249ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995634"
---
# <a name="azure-storage-advanced-threat-protection"></a>Rozšířená ochrana před internetovými útoky Azure Storage

Azure Storage Advanced Threat Protection detekuje anomálie v aktivitě účtu a upozorňuje na potenciálně nebezpečné pokusy o přístup k vašemu účtu. Tato úroveň ochrany lze řešení ohrožení, aniž byste museli být odborné zabezpečení nebo spravovat systémy monitorování zabezpečení.

Hrozby jsou prezentované definováním výstrahy zabezpečení, které aktivují, když dojde k anomálie v aktivitě. Tyto výstrahy integrovat [Azure Security Center](https://azure.microsoft.com/services/security-center/) mezi které patří podrobné údaje o podezřelé aktivitě a doporučení o tom, jak vyšetřením a odstraněním hrozeb. 

> [!NOTE]
> Azure Storage Advanced Threat Protection je momentálně dostupná jenom pro službu Blob service. Výstrahy zabezpečení jsou integrované s Azure Security Center a jsou odeslány e-mailem správcům předplatného.

Azure Storage Advanced Threat Protection ingestuje diagnostické protokoly pro čtení, zápis a odstranění žádosti o službu Blob service pro detekci hrozeb. K prozkoumání výstrahy z rozšířené ochrany před internetovými útoky, budete muset [nakonfigurovat diagnostické protokoly](storage-monitor-storage-account.md#configure-logging) umožňuje všechny úrovně protokolování pro službu Blob service.

## <a name="set-up-advanced-threat-protection-in-the-portal"></a>Nastavení rozšířené ochrany před internetovými útoky na portálu

1. Spuštění webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com/).

2. Přejděte na stránku konfigurace účtu služby Azure Storage, který chcete chránit. V **nastavení** stránce **Advanced Threat Protection**.

3. V **Advanced Threat Protection** okno Konfigurace
    * Zapnout **ON** Advanced *před internetovými útoky*
    * Klikněte na tlačítko **Uložit** nové nebo aktualizované zásady rozšířené ochrany před internetovými útoky uložit.

![Zapnout rozšířené ochrany před internetovými útoky Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## <a name="explore-anomalies"></a>Prozkoumejte anomálií

Pokud dojde k úložiště zvláštností v činnosti, dostanete oznámení e-mailu s informacemi o podezřelé události zabezpečení. Podrobnosti o události patří:

* povaha anomálii
* Název účtu úložiště
* Typ úložiště
* čas události

E-mail také obsahuje podrobnosti o možných příčinách a doporučených akcích pro šetření a zmírnění potenciálního ohrožení.

![Úložiště Azure advanced threat protection e-mailové upozornění](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Můžete prohlížet a spravovat své aktuální výstrahy zabezpečení ze služby Azure Security Center [dlaždice výstrahy zabezpečení](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). Kliknutím na konkrétní výstrahu poskytuje podrobnosti a akce pro zkoumání aktuální hrozby a adresování budoucími hrozbami.

![Úložiště Azure advanced threat protection e-mailové upozornění](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Oznámení ochrany

Výstrahy jsou generovány a potenciálně nebezpečné pokusy o přístup nebo zneužití účtů úložiště. Tyto události můžete spustit následující upozornění:

* **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k účtu úložiště. Například když někdo použil účet úložiště z neobvyklé geografické lokality. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo operace údržby pro vývojáře). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník atd.).

* **Neobvyklé extrakce**: Tato výstraha se aktivuje, když dojde ke změně vzoru extrakce dat z účtu úložiště. Například pokud někdo použil neobvyklé množství dat v účtu úložiště. V některých případech výstraha detekuje legitimní akci (činnosti údržby). V jiných případech výstraha detekuje škodlivou akci (průsak ven dat nebo porušení zabezpečení, neoprávněné přenos dat).

* **Neobvyklý anonymní přístup:** Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k účtu úložiště. Například předpokládejme, že anonymně někdo použil účet úložiště. V některých případech výstraha detekuje legitimní přístupu pomocí veřejné oprávnění ke čtení. V jiných případech výstraha detekuje neoprávněnému přístupu, který zneužívá veřejné oprávnění ke čtení pro kontejner a jeho objekty BLOB.

* **Neočekávané delete:** Tato výstraha se aktivuje, když dojde k jednu nebo více operací neočekávané delete v účtu úložiště, na základě historické analýzy účtu úložiště. Například předpokládejme, že někdo provést *DeleteBlob* operace s použitím nové aplikace a z nové IP adresy. V některých případech výstraha detekuje legitimní akci (správce použít jiný prohlížeč při cestování na firmy). V jiných případech výstraha detekuje škodlivou akci (útočník odstranění dat). 
 
* **Změny oprávnění pro přístup:** Tato výstraha se aktivuje, když je neočekávaná Změna oprávnění pro přístup k účtu úložiště. Předpokládejme například, že někdo změnil přístupová oprávnění k účtu úložiště pomocí nové aplikace a z nové IP adresy. V některých případech výstraha detekuje legitimní akci (správce použít jiný prohlížeč při cestování na firmy). V jiných případech výstraha detekuje škodlivou akci (například útočník zvýšení oprávnění, které získali přístup k účtu). 

* **Nahrát balíček cloudové služby Azure:** Tato výstraha se aktivuje při neočekávané nahrání balíčku Azure Cloud Service (*.cspkg* soubor) do účtu úložiště. Předpokládejme například, *.cspkg* soubor byl odeslán z nové IP adresy. V některých případech výstraha detekuje legitimní akci. V jiných případech výstraha detekuje škodlivou akci (třeba do cloudového balíčku byl odeslán při přípravě na nasazení škodlivé služby).    
   

## <a name="next-steps"></a>Další postup

* Další informace o [protokoly v účtech Azure Storage ](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Další informace o [Azure Security Center](../../security-center/security-center-intro.md)