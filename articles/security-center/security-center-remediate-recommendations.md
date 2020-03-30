---
title: Náprava doporučení v Azure Security Center | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak napravit doporučení v Centru zabezpečení Azure k ochraně vašich prostředků a dodržování zásad zabezpečení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603502"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Zpracování doporučení ve službě Azure Security Center

Doporučení vám dávají návrhy, jak lépe zabezpečit své zdroje. Doporučení implementujete podle kroků nápravy uvedených v doporučení.

## <a name="remediation-steps"></a>Kroky nápravy<a name="remediation-steps"></a>

Po přezkoumání všech doporučení, rozhodnout, který z nich k nápravě jako první. Doporučujeme použít [vliv na zabezpečené skóre,](security-center-recommendations.md#monitor-recommendations) abyste nejprve upřednostnili, co dělat.

1. V seznamu klikněte na doporučení.

1. Postupujte podle pokynů v části **Kroky nápravy.** Každé doporučení má vlastní sadu pokynů. Následující snímek obrazovky ukazuje nápravné kroky pro konfiguraci aplikací tak, aby povolovaly provoz pouze přes protokol HTTPS.

    ![Podrobnosti doporučení](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Po dokončení se zobrazí oznámení informující o tom, zda je náprava úspěšná.

## <a name="quick-fix-remediation"></a>Rychlá oprava nápravy<a name="one-click"></a>

Rychlá oprava umožňuje rychle opravit doporučení pro více prostředků. Je k dispozici pouze pro konkrétní doporučení. Rychlá oprava zjednodušuje nápravu a umožňuje rychle zvýšit skóre zabezpečení a zlepšit zabezpečení vašeho prostředí.

Implementace sanace rychlé opravy:

1. Ze seznamu doporučení, která mají **rychlou opravu!** štítek, klikněte na doporučení.

    [![Vyberte rychlou opravu!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Na kartě **Zdroje není** vyberte prostředky, na kterých chcete doporučení implementovat, a klepněte na tlačítko **Napravit**.

    > [!NOTE]
    > Některé uvedené prostředky mohou být zakázány, protože nemáte příslušná oprávnění k jejich úpravám.

1. V potvrzovacím poli si přečtěte podrobnosti a důsledky nápravy.

    ![Rychlá oprava](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Důsledky jsou uvedeny v šedém poli v okně **Prostředky nápravy,** které se otevře po klepnutí na tlačítko **Náprava**. Uvádějí, jaké změny se dějí při pokračování v nápravě rychlé opravy.

1. V případě potřeby vložte příslušné parametry a schvalte nápravu.

    > [!NOTE]
    > Po dokončení nápravy může trvat několik minut, než se zobrazí prostředky na kartě **Zdroje v pořádku.** Chcete-li zobrazit nápravné akce, zkontrolujte [protokol aktivit](#activity-log).

1. Po dokončení se zobrazí oznámení informující o tom, zda je náprava úspěšná.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Rychlé oprava protokolování nápravv protokolování aktivit<a name="activity-log"></a>

Operace nápravy používá nasazení šablony nebo volání rozhraní REST PATCH API k použití konfigurace na prostředek. Tyto operace jsou zaznamenány v [protokolu aktivit Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="recommendations-with-quick-fix-remediation"></a>Doporučení s rychlou nápravou

|Doporučení|Důsledky|
|---|---|
|Auditování na serverech SQL by mělo být povoleno|Tato akce umožní auditování SQL na těchto serverech a jejich databázích. <br>**Poznámka**: <ul><li>Pro každou oblast vybraných serverů SQL vytvoří a sdílejí všechny servery v této oblasti účet úložiště pro ukládání protokolů auditu.</li><li>Chcete-li zajistit správné auditování, neodstraňujte ani nepřejmenovávejte skupinu prostředků nebo účty úložiště.</li></ul>|
|Rozšířené zabezpečení dat by mělo být povoleno ve vašich instancích spravovaných SQL.|Tato akce umožní sql advanced data security (ADS) na vybraných instancích spravovaných SQL. <br>**Poznámka**: <ul><li>Pro každou oblast a skupinu prostředků vybraných instancí spravovaných SQL vytvoří a budou všechny instance v této oblasti vytvořit a sdílet účet úložiště pro ukládání výsledků prohledávaní.</li><li> ADS se účtuje ve výši $15 za inspravanou instanci SQL.</li></ul>|
|Posouzení ohrožení zabezpečení by mělo být povoleno ve vašich instancích spravovaných SQL.|Tato akce umožní posouzení ohrožení zabezpečení SQL u vybraných instancí spravovaných SQL. <br>**Poznámka**:<ul><li>Posouzení ohrožení zabezpečení SQL je součástí balíčku SQL Advanced Data Security (ADS). Pokud ADS ještě není povolena, bude automaticky povolena ve spravované instanci.</li><li>Pro každou oblast a skupinu prostředků vybraných instancí spravovaných SQL vytvoří a budou všechny instance v této oblasti vytvořit a sdílet účet úložiště pro ukládání výsledků prohledávaní.</li><li>ADS se účtuje ve výši $ 15 za SQL server.</li></ul>||
|Rozšířené zabezpečení dat by mělo být povoleno na serverech SQL|Tato akce umožní rozšířené zabezpečení dat (ADS) na těchto vybraných serverech a jejich databázích. <br>**Poznámka**:<ul><li>Pro každou oblast a skupinu prostředků vybraných serverů SQL vytvoří a sdílejí všechny servery v této oblasti účet úložiště pro ukládání výsledků prohledávaní <.</li><li>ADS se účtuje ve výši $ 15 za SQL server.</li></ul>||
|Posouzení ohrožení zabezpečení by mělo být povoleno na serverech SQL|Tato akce umožní posouzení ohrožení zabezpečení SQL na těchto vybraných serverech a jejich databázích. <br>**Poznámka**:<ul><li>Posouzení ohrožení zabezpečení SQL je součástí balíčku SQL Advanced Data Security (ADS). Pokud ADS ještě není povolena, bude automaticky povolena na serveru SQL.</li><li>Pro každou oblast a skupinu prostředků vybraných serverů SQL vytvoří a budou všechny instance v této oblasti vytvořit a sdílet účet úložiště pro ukládání výsledků prohledávače.</li><li>ADS se účtuje ve výši $ 15 za SQL server.</li></ul>||
|Mělo by být povoleno transparentní šifrování dat v databázích SQL.|Tato akce umožňuje sql database transparent data encryption (TDE) ve vybraných databázích. <br>**Poznámka:** Ve výchozím nastavení budou použity klíče TDE spravované službou.
|Měl by se povolit zabezpečený přenos do účtů úložiště|Tato akce aktualizuje zabezpečení účtu úložiště tak, aby umožňovala požadavky pouze zabezpečená připojení. (HTTPS). <br>**Poznámka**:<ul><li>Všechny požadavky používající protokol HTTP budou odmítnuty.</li><li>Pokud používáte službu Soubory Azure, připojení bez šifrování se nezdaří, včetně scénářů pomocí SMB 2.1, SMB 3.0 bez šifrování a některé varianty klienta Linux SMB. Další informace.</li></ul>|
|Webová aplikace by měla být přístupná pouze přes protokol HTTPS.|Tato akce přesměruje veškerý provoz z protokolu HTTP na protokol HTTPS na vybrané prostředky. <br>**Poznámka**:<ul><li>Koncový bod HTTPS, který nemá certifikát SSL, se zobrazí v prohlížeči s chybou ochrany osobních údajů. Uživatelé, kteří mají vlastní doménu, tedy musí ověřit, zda mají nastaven certifikát SSL.</li><li>Ujistěte se, že brány firewall paketů a webových aplikací chrání tekutou službu, povolte předávání relací HTTPS.</li></ul>|
|Aplikace function by měla být přístupná pouze přes protokol HTTPS|Tato akce přesměruje veškerý provoz z protokolu HTTP na protokol HTTPS na vybrané prostředky. <br>**Poznámka**:<ul><li>Koncový bod HTTPS, který nemá certifikát SSL, se zobrazí v prohlížeči s chybou ochrany osobních údajů. Uživatelé, kteří mají vlastní doménu, tedy musí ověřit, zda mají nastaven certifikát SSL.</li><li>Ujistěte se, že brány firewall paketů a webových aplikací chrání tekutou službu, povolte předávání relací HTTPS.</li></ul>|
|Aplikace API by měla být přístupná pouze přes protokol HTTPS|Tato akce přesměruje veškerý provoz z protokolu HTTP na protokol HTTPS na vybrané prostředky. <br>**Poznámka**:<ul><li>Koncový bod HTTPS, který nemá certifikát SSL, se zobrazí v prohlížeči s chybou ochrany osobních údajů. Uživatelé, kteří mají vlastní doménu, tedy musí ověřit, zda mají nastaven certifikát SSL.</li><li>Ujistěte se, že brány firewall paketů a webových aplikací chrání tekutou službu, povolte předávání relací HTTPS.</li></ul>|
|Vzdálené ladění by mělo být vypnuto pro webovou aplikaci.|Tato akce zakáže vzdálené ladění.|
|Vzdálené ladění by mělo být vypnuto pro aplikaci Function App|Tato akce zakáže vzdálené ladění.|
|Vzdálené ladění by mělo být vypnuto pro aplikaci API.|Tato akce zakáže vzdálené ladění.|
|CORS by neměl povolit přístup ke webové aplikaci pro každý prostředek.|Tato akce blokuje přístup k webové aplikaci jiným doménám. Chcete-li povolit určité domény, zadejte je do pole Povolený původ (oddělené čárkami). <br>**Poznámka:** Ponechání pole prázdného zablokuje všechna volání mezi počátky.'Název pole Param: "Povolené počátky"|
|CORS by neměl a mj.|Tato akce blokuje jiné domény v přístupu k aplikaci funkce. Chcete-li povolit určité domény, zadejte je do pole Povolený původ (oddělené čárkami). <br>**Poznámka:** Ponechání pole prázdného zablokuje všechna volání mezi počátky.'Název pole Param: "Povolené počátky"|
|CORS by neměl a) neumožňovat všem prostředkům přístup k vaší aplikaci API.|Tato akce blokuje přístup k aplikaci rozhraní API jiným doménám. Chcete-li povolit určité domény, zadejte je do pole Povolený původ (oddělené čárkami). <br>**Poznámka:** Ponechání pole prázdného zablokuje všechna volání mezi počátky.'Název pole Param: "Povolené počátky"|
|Agent monitorování by měl být povolen na virtuálních počítačích.|Tato akce nainstaluje agenta monitorování na vybrané virtuální počítače. Vyberte pracovní prostor pro agenta, který má být podaný.<ul><li>Pokud je zásada aktualizace nastavena na automatické, nasadí se na nové existující instance.</li><li>Pokud je zásada aktualizace nastavena na ruční a chcete agenta nainstalovat do existujících instancí, zaškrtněte políčko. [Další informace](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Diagnostické protokoly v trezoru klíčů by měly být povoleny|Tato akce umožňuje diagnostické protokoly v trezorech klíčů. Diagnostické protokoly a metriky jsou uloženy ve vybraném pracovním prostoru.|
|Diagnostické protokoly v sběrnici service by měly být povoleny|Tato akce umožňuje diagnostické protokoly na sběrnici. Diagnostické protokoly a metriky jsou uloženy ve vybraném pracovním prostoru.|

## <a name="next-steps"></a>Další kroky

V tomto dokumentu se vám ukázali, jak v Centru zabezpečení obnovit doporučení. Další informace o Centru zabezpečení najdete v následujících tématech:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Monitorování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak sledovat stav prostředků Azure.
