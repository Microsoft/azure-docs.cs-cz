---
title: Správa výstrah zabezpečení ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže používat funkce služby Azure Security Center ke správě výstrah zabezpečení a reagování na ně.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: rkarlin
ms.openlocfilehash: 50fa467a6405fdc6b99c78a8f57411abf3be6336
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836623"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Správa a zpracování výstrah zabezpečení v Azure Security Center
Tento dokument vám pomůže používat Azure Security Center ke správě výstrah zabezpečení a reagování na ně.

> [!NOTE]
> Pokud chcete povolit rozšířené detekce, upgradujte na Azure Security Center Standard. Bezplatná zkušební verze je k dispozici. Pokud chcete provést upgrade, vyberte v [zásadách zabezpečení](security-center-azure-policy.md) cenovou úroveň. Další informace najdete v článku o [cenách Azure Security Center](security-center-pricing.md).
>
>

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, aby se zjistily skutečné hrozby a snížil počet falešných poplachů. Seznam upřednostňovaných výstrah zabezpečení se zobrazí ve službě Security Center spolu s informacemi, které potřebujete k rychlému prozkoumání problému, a doporučeními týkajícími se řešení útoku.


> [!NOTE]
> Další informace o tom, jak detekce služby Security Center pracuje, najdete v článku [Funkce detekce ve službě Azure Security Center](security-center-detection-capabilities.md).
>
>

## <a name="managing-security-alerts"></a>Správa výstrah zabezpečení
Aktuální výstrahy můžete zkontrolovat jejich prohlížením na dlaždici **Výstrahy zabezpečení**. Pomocí uvedených pokynů níže zobrazíte o každé výstraze další podrobnosti:

1. Na řídicím panelu Security Center vidíte dlaždici **Výstrahy zabezpečení**.

    ![Dlaždice Výstrahy zabezpečení ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. Kliknutím na dlaždici otevřete **Výstrahy zabezpečení** s dalšími podrobnostmi o výstrahách.

   ![Výstrahy zabezpečení ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

V dolní části této stránky jsou uvedené podrobnosti pro každou výstrahu. Chcete-li je seřadit, klikněte na sloupec, podle kterého chcete řadit. Definice pro každý sloupec je uvedená dále:

* **Description** (Popis): Stručné vysvětlení výstrahy.
* **Count** (Počet): Seznam všech výstrah tohoto konkrétního typu, které byly zjištěny v určitý den.
* **Detected by** (Zjistil): Služba, která je zodpovědná za aktivaci výstrahy.
* **Date** (Datum): Datum, kdy došlo k události.
* **State** (Stav): Aktuální stav výstrahy. Existují dva typy stavů:
  * **Active** (Aktivní): Výstraha zabezpečení byla zjištěna.
  * **Dismissed** (Zamítnuto): Výstraha zabezpečení byla uživatelem zamítnuta. Tento stav se obvykle používá pro výstrahy, které byly prozkoumány a buď zmírněny, nebo nedošlo ke skutečnému útoku.
* **Severity** (Závažnost): Úroveň závažnosti, tj. vysoká, střední nebo nízká.

> [!NOTE]
> Výstrahy zabezpečení, které generuje Security Center, se také zobrazí v části Protokol aktivit Azure. Další informace o tom, jak získat přístup k Protokolu aktivit Azure, najdete v popisu [zobrazení protokolů aktivit pro auditování akcí u prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
>


### <a name="alert-severity"></a>Závažnost upozornění

> [!NOTE]
> Závažnost výstrahy je jinak zobrazí na portálu a rozhraní REST API, rozdíly jsou popsány v níže uvedeném seznamu.

-   **Vysoká**: je vysoká pravděpodobnost, že dojde k ohrožení vašich prostředků. Byste se podívat do něj okamžitě. Security Center má vysokou spolehlivostí škodlivým činnostem závěry používají k vydávání výstrahu. Například výstrahy, které se zjistí spuštění známé škodlivé nástroje, jako je nástroj Mimikatz, běžné nástroj používaný pro krádež přihlašovacích údajů. 
-   **Střední (nízké nároky na rozhraní REST API)**: to je pravděpodobně podezřelou aktivitu, která může znamenat, že dojde k ohrožení prostředku.
Security Center důvěru v analytické nebo hledání se střední a spolehlivosti škodlivým činnostem je střední na vysokou. Ty by obvykle neměl machine learning nebo detekce anomálií, na základě. Například znak při pokusu o z neobvyklého umístění.
-   **Nízká (informace v rozhraní REST API)**: může to být neškodné pozitivní nebo zablokování útoku. 
    - Security Center není dostatečně přesvědčeni, že je cílem škodlivý a aktivity mohou být zcela nevinné. Vymazat protokol je například akci, která může dojít v případě, že útočník pokusí skrýt svoje stopy, ale v mnoha případech je běžné operace prováděné správci.
    - Security Center nebude zjistit obvykle při se zablokoval útoky, pokud je zajímavé případ, který doporučujeme, abyste že si prohlédnout. 
-   **Informativní (tichý režim v rozhraní REST API)**: informativní výstrahy se zobrazí pouze, když k podrobnostem na incident zabezpečení, nebo pokud používáte rozhraní REST API s konkrétní výstrahy ID. Incident je obvykle obsahují celou řadu výstrah, z nichž některé mohou zobrazit na svoje vlastní, které bude jenom informativní, ale v kontextu jiných oznámení může být albertových podívat podrobněji. 

### <a name="filtering-alerts"></a>Filtrování výstrah
Výstrahy můžete filtrovat podle data, stavu nebo závažnosti. Filtrování výstrah může být užitečné v případech, kdy potřebujete zúžit obor zobrazených výstrah zabezpečení. Například můžete chtít zabývat se výstrahami zabezpečení, k nimž došlo v posledních 24 hodinách, protože zjišťujete případný průnik do systému.

1. Klikněte na **Filtr** v části **Výstrahy zabezpečení**. Otevře se **Filtr** a vy vyberte hodnoty pro datum, stav a závažnost výstrah, které chcete zobrazit.

    ![Filtrování výstrah ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Reakce na výstrahy zabezpečení
Vyberte výstrahu zabezpečení, abyste se dozvěděli další informace o událostech, které výstrahu vygenerovaly a kroky, pokud existují, které je třeba provést k nápravě útoku. Výstrahy zabezpečení jsou seskupené podle typu a data. Kliknutím na výstrahu zabezpečení se otevře stránka obsahující seznam seskupených výstrah.

![Reakce na výstrahy zabezpečení v Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

V tomto případě odkazují vygenerované výstrahy na podezřelé aktivity protokolu RDP (Remote Desktop Protocol). První sloupec zobrazuje, které prostředky byly napadeny, druhý zobrazuje počet útoků, třetí čas útoku, čtvrtý stav výstrahy a pátý závažnost útoku. Po zkontrolování těchto informací klikněte na prostředek, který byl napaden.

![Návrhy postupu při výstrahách zabezpečení v Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

V poli **Popis** najdete další podrobnosti o této události. Tyto další podrobnosti nabízejí získání náhledu na příčinu výstrahy zabezpečení, cílový prostředek, případnou zdrojovou IP adresu a doporučení, jak provést nápravu.  V některých případech je zdrojová IP adresa prázdná (nedostupná), protože ne všechny protokoly událostí zabezpečení Windows obsahují IP adresu.

Náprava navrhovaná službou Security Center se liší podle výstrahy zabezpečení. V některých případech budete muset k implementaci doporučené nápravy použít další možnosti Azure. Například nápravou pro tento útok je uvedení IP adresy, která generuje tento útok, v seznamu zakázaných serverů, a to pomocí [seznamu ACL sítě](../virtual-network/virtual-networks-acl.md) nebo pravidla [skupiny zabezpečení sítě](../virtual-network/security-overview.md#security-rules). Další informace o různých typech výstrah najdete v tématu [Výstrahy zabezpečení podle typu v Azure Security Center](security-center-alerts-type.md).

> [!NOTE]
> Ve službě Security Center byla vydána ve verzi Limited Preview nová sada detekcí, které využívají záznamy auditu, což je běžný auditní rámec, k detekci škodlivého chování na počítačích s Linuxem. Pokud se chcete připojit k verzi Preview, zašlete [nám](mailto:ASC_linuxdetections@microsoft.com) e-mail s ID vašich předplatných.


## <a name="see-also"></a>Další informace najdete v tématech
V tomto dokumentu jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Řešení bezpečnostních incidentů v Azure Security Center](security-center-incident.md)
* [Funkce detekce ve službě Azure Security Center](security-center-detection-capabilities.md)
* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md)
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
