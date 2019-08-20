---
title: Opravit doporučení v Azure Security Center | Microsoft Docs
description: Tento dokument vysvětluje, jak opravit doporučení v Azure Security Center, která vám pomůžou chránit prostředky Azure a zůstat v souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2019
ms.author: v-mohabe
ms.openlocfilehash: a32e344ffe33f411bae85763ae3b919040c1109b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575623"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Oprava doporučení v Azure Security Center

Doporučení vám poskytnou návrhy na to, jak lépe zabezpečit prostředky.  Doporučení můžete implementovat podle kroků pro nápravu, které jsou uvedené v doporučení. 

## Postup odstranění problému<a name="remediation-steps"></a>

Po kontrole všech doporučení se rozhodněte, která z nich se má opravit jako první. Doporučujeme, abyste používali [dopad bezpečného skóre](security-center-recommendations.md#monitor-recommendations) , abyste lépe určili prioritu toho, co udělat jako první.

1. V seznamu klikněte na doporučení.
1. Postupujte podle pokynů v části **Postup odstranění problému** . Každé doporučení má svou vlastní sadu instrukcí. Níže vidíte postup, jak nakonfigurovat aplikace tak, aby povolovaly jenom přenosy přes protokol HTTPS.

    ![Podrobnosti doporučení](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Po dokončení se zobrazí oznámení o tom, jestli se oprava úspěšně provedla.

## Oprava nápravy jedním kliknutím (Preview)<a name="one-click"></a>

Oprava jedním kliknutím umožňuje opravit doporučení na hromadných prostředcích jediným kliknutím. Tato možnost je dostupná jenom pro konkrétní doporučení. Oprava jedním kliknutím zjednodušuje nápravu a umožňuje rychle vylepšit vaše zabezpečené skóre a zvýšit zabezpečení vašeho prostředí.

Implementace opravy jedním kliknutím:

1. V seznamu doporučení, která mají popisek **1 klikněte-na opravit** , klikněte na doporučení.  

   ![Vybrat opravu jedním kliknutím](./media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)

2. Na kartě **poškozené prostředky** vyberte prostředky, u kterých chcete doporučení implementovat, a klikněte na **opravit**. 

    > [!NOTE]
    > Některé z uvedených prostředků můžou být zakázané, protože nemáte příslušná oprávnění k jejich úpravám.

3. V potvrzovacím poli si přečtěte podrobnosti o nápravě a důsledky. 

   ![Oprava jedním kliknutím](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Důsledky jsou uvedeny v poli šedé v okně **opravit prostředky** , které se otevře po kliknutí na **opravit**. Seznamte se s tím, k jakým změnám dochází, když budete pokračovat s jedním kliknutím na nápravu.

4. V případě potřeby vložte příslušné parametry a schvalte nápravu.

    > [!NOTE]
    > Tato operace může trvat několik minut, než se dokončí náprava, aby se na kartě **prostředky v pořádku** zobrazily prostředky. Chcete-li zobrazit nápravné akce, zkontrolujte [Protokol aktivit](#activity-log).

5. Po dokončení se zobrazí oznámení o tom, jestli se oprava úspěšně provedla.

## Protokolování oprav jedním kliknutím v protokolu aktivit<a name="activity-log"></a>

Operace opravy používá volání rozhraní API pro nasazení šablony nebo opravu REST API k aplikování konfigurace na prostředku. Tyto operace se zaznamenávají do [protokolu aktivit Azure](../azure-resource-manager/resource-group-audit.md).


## <a name="recommendations-with-one-click-remediation"></a>Doporučení s jedním kliknutím na nápravu

|Doporučení|Nepřímo|
|---|---|
|Auditování na serverech SQL by mělo být povoleno.|Tato akce umožní auditování SQL na těchto serverech a jejich databázích. <br>**Poznámka:** <ul><li>Pro každou oblast vybraných serverů SQL se vytvoří účet úložiště pro ukládání protokolů auditu a sdílí se se všemi servery v této oblasti.</li><li>Chcete-li zajistit správné auditování, neodstraňujte ani neměňte skupinu prostředků ani účty úložiště.</li></ul>|
|Pro spravované instance SQL by mělo být povolené rozšířené zabezpečení dat|Tato akce povolí SQL Advanced Data Security (ADS) na vybraných instancích SQL. <br>**Poznámka:** <ul><li>Pro každou oblast a skupinu prostředků vybraných spravovaných instancí SQL se vytvoří účet úložiště pro ukládání výsledků kontroly a sdílí se se všemi instancemi v této oblasti.</li><li> REKLAMY se účtují na základě spravované instance SQL $15.</li></ul>|
|Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení|Tato akce povolí posouzení ohrožení zabezpečení SQL na vybraných instancích SQL. <br>**Poznámka:**<ul><li>Posouzení ohrožení zabezpečení SQL je součástí balíčku SQL Advanced Data Security (ADS). Pokud již není služba ADS povolena, bude na spravované instanci automaticky povolena.</li><li>Pro každou oblast a skupinu prostředků vybraných spravovaných instancí SQL se vytvoří účet úložiště pro ukládání výsledků kontroly a sdílí se se všemi instancemi v této oblasti.</li><li>Na reklamu se účtuje $15 na SQL Server.</li></ul>||
|Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat|Tato akce povolí rozšířené zabezpečení dat (ADS) na těchto vybraných serverech a jejich databázích. <br>**Poznámka:**<ul><li>Pro každou oblast a skupinu prostředků vybraných serverů SQL se vytvoří účet úložiště pro ukládání výsledků kontroly a sdílí se se všemi servery v této oblasti. <</li><li>Na reklamu se účtuje $15 na SQL Server.</li></ul>||
|Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení|Tato akce povolí posouzení ohrožení zabezpečení SQL na těchto vybraných serverech a jejich databázích. <br>**Poznámka:**<ul><li>Posouzení ohrožení zabezpečení SQL je součástí balíčku SQL Advanced Data Security (ADS). Pokud již není služba ADS povolena, bude na serveru SQL Server automaticky povolena.</li><li>Pro každou oblast a skupinu prostředků vybraných serverů SQL se vytvoří účet úložiště pro ukládání výsledků kontroly a sdílí se se všemi instancemi v této oblasti.</li><li>Na reklamu se účtuje $15 na SQL Server.</li></ul>||
|Mělo by být povoleno transparentní šifrování dat u databází SQL.|Tato akce povolí ve vybraných databázích SQL Database transparentní šifrování dat (TDE). <br>**Poznámka:** Ve výchozím nastavení budou použity klíče TDE spravované službou. 
|Zabezpečený přenos do účtů úložiště by měl být povolený.|Tato akce aktualizuje zabezpečení účtu úložiště tak, aby povolovala požadavky pouze pomocí zabezpečených připojení. (HTTPS). <br>**Poznámka:**<ul><li>Všechny požadavky využívající protokol HTTP budou odmítnuty.</li><li>Pokud používáte službu souborů Azure, připojení bez šifrování selže, včetně scénářů využívajících SMB 2,1, SMB 3,0 bez šifrování a některých charakterů klienta SMB systému Linux.  Získejte další informace.</li></ul>|
|Webová aplikace by měla být přístupná jen přes HTTPS|Tato akce přesměruje veškerý provoz z HTTP na HTTPS na vybraných prostředcích. <br>**Poznámka:**<ul><li>Koncový bod HTTPS, který nemá certifikát SSL, se v prohlížeči zobrazí se stavem "Chyba ochrany osobních údajů". Proto si uživatelé, kteří mají vlastní doménu, musí ověřit, že si nastavili certifikát SSL.</li><li>Zajistěte, aby brána firewall paketů a webových aplikací chránila službu App Service a umožnila předávání relací HTTPS.</li></ul>|
|Funkce aplikace by měla být přístupná jen přes HTTPS|Tato akce přesměruje veškerý provoz z HTTP na HTTPS na vybraných prostředcích. <br>**Poznámka:**<ul><li>Koncový bod HTTPS, který nemá certifikát SSL, se v prohlížeči zobrazí se stavem "Chyba ochrany osobních údajů". Proto si uživatelé, kteří mají vlastní doménu, musí ověřit, že si nastavili certifikát SSL.</li><li>Zajistěte, aby brána firewall paketů a webových aplikací chránila službu App Service a umožnila předávání relací HTTPS.</li></ul>|
|Aplikace API by měla být přístupná jen přes protokol HTTPS|Tato akce přesměruje veškerý provoz z HTTP na HTTPS na vybraných prostředcích. <br>**Poznámka:**<ul><li>Koncový bod HTTPS, který nemá certifikát SSL, se v prohlížeči zobrazí se stavem "Chyba ochrany osobních údajů". Proto si uživatelé, kteří mají vlastní doménu, musí ověřit, že si nastavili certifikát SSL.</li><li>Zajistěte, aby brána firewall paketů a webových aplikací chránila službu App Service a umožnila předávání relací HTTPS.</li></ul>|
|Vzdálené ladění by měl být vypnuté pro webovou aplikaci|Tato akce zakáže vzdálené ladění.|
|Vzdálené ladění by mělo být pro Function App vypnuté.|Tato akce zakáže vzdálené ladění.|
|Pro aplikaci API by mělo být vypnuto vzdálené ladění.|Tato akce zakáže vzdálené ladění.|
|CORS by neměl umožňovat každému prostředku přístup k vaší webové aplikaci|Tato akce blokuje přístup k vaší webové aplikaci dalším doménám. Pokud chcete povolit konkrétní domény, zadejte je do pole povolené zdroje (oddělené čárkami). <br>**Poznámka:** Když necháte pole prázdné, zablokuje se všechna volání mezi zdroji. název pole PARAM: ' Povolené zdroje '|
|CORS by nemělo umožňovat každý prostředek pro přístup k aplikaci Function App|Tato akce blokuje přístup k vaší aplikaci funkcí jiným doménám. Pokud chcete povolit konkrétní domény, zadejte je do pole povolené zdroje (oddělené čárkami). <br>**Poznámka:** Když necháte pole prázdné, zablokuje se všechna volání mezi zdroji. název pole PARAM: ' Povolené zdroje '|
|CORS by neměl umožňovat každému prostředku přístup k vaší aplikaci API.|Tato akce zablokuje ostatním doménám přístup k vaší aplikaci API. Pokud chcete povolit konkrétní domény, zadejte je do pole povolené zdroje (oddělené čárkami). <br>**Poznámka:** Když necháte pole prázdné, zablokuje se všechna volání mezi zdroji. název pole PARAM: ' Povolené zdroje '|
|Na virtuálních počítačích by měl být povolený agent monitorování.|Tato akce nainstaluje agenta monitorování na vybrané virtuální počítače. Vyberte pracovní prostor, do kterého má agent nahlásit.<ul><li>Pokud je vaše zásada aktualizace nastavená na hodnotu automaticky, nasadí se na nové existující instance.</li><li>Pokud je vaše zásada aktualizace nastavená na ruční a přejete si nainstalovat agenta na existující instance, zaškrtněte políčko u možnosti zaškrtávací políčko.  [Víc se uč](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Měly by být povolené diagnostické protokoly v Key Vault.|Tato akce povolí diagnostické protokoly pro trezory klíčů. Diagnostické protokoly a metriky se ukládají ve vybraném pracovním prostoru.|
|Měly by být povolené diagnostické protokoly ve službě Service Bus.|Tato akce povolí diagnostické protokoly ve službě Service Bus. Diagnostické protokoly a metriky se ukládají ve vybraném pracovním prostoru.|

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste si ukázali, jak opravit doporučení v Security Center. Další informace o Security Center najdete v následujících tématech:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md): Přečtěte si, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md): Zjistěte, jak monitorovat stav svých prostředků Azure.
