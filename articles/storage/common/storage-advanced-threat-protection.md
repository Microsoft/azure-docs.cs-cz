---
title: Advanced Threat Protection pro Azure Storage
description: Konfigurace Azure Storage Advanced Threat Protection detekovat anomálie v aktivitě účtu a upozornit vás na potenciálně nebezpečné pokusy o přístup k vašemu účtu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: ed263fec271801ac9c46bbc3125c71ca15b9a330
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153972"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection pro Azure Storage

Advanced Threat Protection pro Azure Storage poskytuje další vrstvu analytických nástrojů zabezpečení, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo využití účtů úložiště. Tato úroveň ochrany lze řešení ohrožení, aniž byste museli být odborné zabezpečení nebo spravovat systémy monitorování zabezpečení. 

Pokud dojde k anomálie v aktivitě se aktivují upozornění zabezpečení.  Tyto výstrahy zabezpečení jsou integrované s [Azure Security Center](https://azure.microsoft.com/services/security-center/)a jsou také odesílány e-mailem správci předplatného s podrobnostmi o podezřelé aktivitě a doporučení o tom, jak vyšetřením a odstraněním hrozeb.

> [!NOTE]
> * Rozšířená ochrana před internetovými útoky pro Azure Storage je momentálně dostupná jenom pro úložiště objektů Blob.
> * Cenové podrobnosti, včetně bezplatné 30denní zkušební, najdete v článku [stránce s cenami za Azure Security Center]( https://azure.microsoft.com/pricing/details/security-center/).
> * Ochrana ATP v programu pro funkce Azure storage není aktuálně dostupná v oblastech suverénních cloudů a Azure government.

Rozšířená ochrana před internetovými útoky pro Azure Storage ingestuje diagnostické protokoly pro čtení, zápisu a žádosti o odstranění do úložiště objektů Blob pro detekci hrozeb. K prozkoumání výstrahy z rozšířené ochrany před internetovými útoky, můžete zobrazit aktivitu související úložiště pomocí Storage Analytics protokolování. Další informace najdete v tématu Jak [konfigurace protokolování Storage Analytics](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Nastavení rozšířené ochrany před internetovými útoky 

### <a name="using-the-portal"></a>Použití portálu

1. Spuštění webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com/).

2. Přejděte na stránku konfigurace účtu služby Azure Storage, který chcete chránit. V **nastavení** stránce **Advanced Threat Protection**.

3. V **Advanced Threat Protection** okno Konfigurace
    * Zapnout **ON** Advanced *před internetovými útoky*
    * Klikněte na tlačítko **Uložit** nové nebo aktualizované zásady rozšířené ochrany před internetovými útoky uložit. (Ceny uvedené na obrázku jsou například pouze pro účely.)

![Zapnout rozšířené ochrany před internetovými útoky Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Pomocí Azure Security Center
Když se přihlásíte k odběru na úrovni Standard ve službě Azure Security Center, je nastavení rozšířené ochrany před internetovými útoky na vaše účty úložiště. Další informace najdete v části [upgradovat na Security Center úrovně Standard pro zvýšení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-pricing). (Ceny uvedené na obrázku jsou například pouze pro účely.)

![Standardní úroveň v ASC](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>Pomocí šablon Azure Resource Manageru

Použití šablony Azure Resource Manageru k nasazení účtu služby Azure Storage s Advanced Threat Protection povolená.
Další informace najdete v tématu [účet úložiště se Rozšířená ochrana před internetovými útoky](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Pomocí zásad Azure

Pomocí služby Azure Policy se povolit rozšířenou ochranu před internetovými útoky v účtech úložiště v rámci konkrétní předplatné nebo skupinu prostředků.

1. Něco Azure **zásady – definice** stránky.

1. Hledat **nasazení rozšířené ochrany před internetovými útoky na účty úložiště** zásad.

     ![Hledání zásad](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Vyberte skupinu Azure předplatné nebo prostředek.

    ![Vyberte předplatné nebo skupinu](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Přiřazení zásad.

    ![Stránka definice zásad](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Pomocí rozhraní REST API
Příkazy rozhraní Rest API k vytvoření, aktualizaci nebo získat nastavení rozšířené ochrany před internetovými útoky pro konkrétnímu účtu úložiště.

* [Rozšířená ochrana před internetovými útoky – vytvořit](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Rozšířená ochrana před internetovými útoky - Get](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Pomocí následujících rutin prostředí PowerShell:

  * [Povolit rozšířené ochrany před internetovými útoky](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Získáte tak rozšířené ochrany před internetovými útoky](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Zakázat rozšířené ochrany před internetovými útoky](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Prozkoumejte bezpečnostních anomálií

Pokud dojde k úložiště zvláštností v činnosti, dostanete oznámení e-mailu s informacemi o podezřelé události zabezpečení. Podrobnosti o události patří:

* Povaha anomálii
* Název účtu úložiště
* Čas události
* Typ úložiště
* Možné příčiny 
* Kroky šetření
* Nápravných kroků


E-mail také obsahuje podrobnosti o možných příčinách a doporučených akcích pro šetření a zmírnění potenciálního ohrožení.

![Úložiště Azure advanced threat protection e-mailové upozornění](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Můžete prohlížet a spravovat své aktuální výstrahy zabezpečení ze služby Azure Security Center [dlaždice výstrahy zabezpečení](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). Kliknutím na konkrétní výstrahu poskytuje podrobnosti a akce pro zkoumání aktuální hrozby a adresování budoucími hrozbami.

![Úložiště Azure advanced threat protection e-mailové upozornění](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Oznámení ochrany

Výstrahy jsou generovány a potenciálně nebezpečné pokusy o přístup nebo zneužití účtů úložiště. Tyto události můžete spustit následující upozornění:

### <a name="anomalous-access-pattern-alerts"></a>Neobvyklé přístup vzor výstrahy

* **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k účtu úložiště. Například když někdo použil účet úložiště z neobvyklé geografické lokality.
Možné příčiny:
   * Útočník získal přístup k účtu úložiště
   * Oprávněný uživatel přístup k účtu úložiště z nového místa
 
* **Aplikace anomálií**: Tato výstraha indikuje neobvyklou aplikace má získat přístup k tomuto účtu úložiště. Možné příčiny:
   * Útočník má získat přístup k účtu úložiště pomocí nové aplikace.
   * Oprávněný uživatel použil nové aplikace a prohlížeče pro přístup k účtu úložiště.

* **Anonymní přístup**: Tato výstraha indikuje, že dojde ke změně vzoru přístupu k účtu úložiště. Například tento účet byl přistupovat anonymně (tj. bez ověřování), což neočekávaný porovnává se vzorem poslední přístup na tento účet.
Možné příčiny:
   * Útočník zneužije veřejné oprávnění ke čtení pro kontejner.
   * Veřejné oprávnění ke čtení pro kontejner se používá legitimní uživatel nebo aplikace.

### <a name="anomalous-extractupload-alerts"></a>Výstrahy na neobvyklé extrahování a nahrávání

* **Průsak dat ven**: Tato výstraha indikuje, že má byly extrahovány neobvykle velký objem dat v porovnání s poslední aktivitu na tento kontejner úložiště. Možné příčiny:
   * Útočník má extrahovat velké množství dat z kontejneru. (Příklad: průsak ven dat nebo porušení zabezpečení, neoprávněné přenos dat)
   * Oprávněné uživatele nebo aplikace má extrahovat neobvyklé množství dat z kontejneru. (Příklad: činnosti údržby)

* **Neočekávané odstranit**: Tato výstraha indikuje, že jednu nebo více operací neočekávané odstranit došlo v účtu úložiště, ve srovnání s poslední aktivitu na tento účet. Možné příčiny:
   * Útočník odstranila data z vašeho účtu úložiště.
   * Oprávněný uživatel provedl neobvyklé odstranění.

* **Nahrání balíčku Azure Cloud Service**: Tato výstraha indikuje, že k Azure Cloud Service balíček (.cspkg soubor) se odeslal do účtu úložiště neobvyklým způsobem, ve srovnání s poslední aktivitu na tento účet. Možné příčiny: 
   * Útočník má se připravuje se nasazení škodlivý kód z vašeho účtu úložiště do cloudové služby Azure.
   * Oprávněný uživatel byl Příprava na nasazení oprávněné služby.

### <a name="suspicious-storage-activities-alerts"></a>Upozornění úložiště podezřelé aktivity

* **Přístup k oprávnění změnit**: Tato výstraha indikuje, že oprávnění tohoto kontejneru úložiště se změnily neobvyklým způsobem. Možné příčiny: 
   * Útočník má změnit oprávnění kontejneru na oslabit zabezpečení.
   * Oprávněný uživatel změnil kontejneru oprávnění.

* **Přístup ke kontrole**: Tato výstraha indikuje, že oprávnění přístupu k účtu úložiště byly podrobeny neobvyklým způsobem, ve srovnání s poslední aktivitu na tento účet. Možné příčiny: 
   * Útočník provedl rekognoskace pro budoucí útoku.
   * Oprávněný uživatel provedl údržby v účtu úložiště.

* **Zkoumání dat**: Tato výstraha indikuje, že objekty BLOB nebo kontejnery v účtu úložiště byly uvedené neobvyklým způsobem, ve srovnání s poslední aktivitu na tento účet. Možné příčiny: 
   * Útočník provedl rekognoskace pro budoucí útoku.
   * Oprávněné uživatele nebo aplikace logiky má prozkoumat data v rámci účtu úložiště.






## <a name="next-steps"></a>Další postup

* Další informace o [protokoly v účtech Azure Storage](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Další informace o [Azure Security Center](../../security-center/security-center-intro.md)
