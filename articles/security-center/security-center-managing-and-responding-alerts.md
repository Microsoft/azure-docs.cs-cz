---
title: Správa výstrah zabezpečení ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže používat funkce služby Azure Security Center ke správě výstrah zabezpečení a reagování na ně.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: v-mohabe
ms.openlocfilehash: 39849514d772f128434daad590de22f941245af7
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516107"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Správa a zpracování výstrah zabezpečení v Azure Security Center
Tento dokument vám pomůže používat Azure Security Center ke správě výstrah zabezpečení a reagování na ně.

> [!NOTE]
> Pokud chcete povolit rozšířené detekce, upgradujte na Azure Security Center Standard. K dispozici je bezplatná zkušební verze. Pokud chcete provést upgrade, vyberte v [zásadách zabezpečení](tutorial-security-policy.md) cenovou úroveň. Další informace najdete v článku o [cenách Azure Security Center](security-center-pricing.md).
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

* **Popis**: Stručné vysvětlení výstrahy.
* **Počet**: Seznam všech výstrah tohoto konkrétního typu, které byly zjištěny v určitém dni.
* **Zjistil**: Služba, která byla zodpovědná za aktivaci výstrahy.
* **Datum**: Datum, kdy došlo k události.
* **Stav**: Aktuální stav této výstrahy. Existují dva typy stavů:
  * **Aktivní**: Bylo zjištěno upozornění zabezpečení.
  * **Neúspěšné**: Uživatel zrušil výstrahu zabezpečení. Tento stav se obvykle používá pro výstrahy, které byly prověřené a buď zmírňované, nebo nezjištěné jako skutečný útok.
* **Závažnost**: Úroveň závažnosti, která může být vysoká, střední nebo nízká.

> [!NOTE]
> Výstrahy zabezpečení, které generuje Security Center, se také zobrazí v části Protokol aktivit Azure. Další informace o tom, jak získat přístup k Protokolu aktivit Azure, najdete v popisu [zobrazení protokolů aktivit pro auditování akcí u prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
>


### <a name="alert-severity"></a>Závažnost upozornění

-   **Vysoká**: Existuje vysoká pravděpodobnost, že dojde k ohrožení zabezpečení prostředku. Měli byste se podívat hned. Security Center má vysokou důvěru v škodlivém záměru i ve zjištěních použitých k vystavení výstrahy. Například výstraha, která detekuje spuštění známého škodlivého nástroje, jako je například Mimikatz, společný nástroj používaný ke krádeži přihlašovacích údajů. 
-   **Střední**: Je pravděpodobné, že se jedná o podezřelou aktivitu, která může znamenat, že došlo k ohrožení zabezpečení prostředku.
Při analýze nebo hledání je jistota Security Center střední a důvěra škodlivého záměru je střední až vysoká. Obvykle se jedná o strojové učení nebo detekci na základě anomálií. Například pokus o přihlášení z umístění neobvyklé.
-   **Nízká úroveň**: Může se jednat o neškodný pozitivní nebo blokovaný útok. 
    - Security Center není dostatečně jistý, že záměr je škodlivý a aktivita může být Innocent. Například vymazání protokolu je akce, ke které může dojít, když se útočník pokusí skrýt jejich stopy, ale v mnoha případech je rutinní operací prováděná správci.
    - Security Center vám většinou neřekne při zablokování útoků, pokud se nejedná o zajímavý případ, který doporučujeme najít. 
-   **Informační**: Informační výstrahy se zobrazí jenom v případě, že přejdete k incidentu zabezpečení, nebo pokud použijete REST API s konkrétním ID výstrahy. Incident se většinou skládá z několika výstrah. některé z nich se můžou zobrazovat jenom s informacemi, ale v kontextu dalších výstrah se můžou důvěryhodného o bližším vzhledu.  

> [!NOTE]
> Pokud používáte verzi rozhraní API **2015-06-01-Preview** , existují rozdíly v tom, jaké typy závažnosti alarmu jsou aplikovány na scénáře, od výše uvedeného.  

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

Náprava navrhovaná službou Security Center se liší podle výstrahy zabezpečení. V některých případech budete muset k implementaci doporučené nápravy použít další možnosti Azure. Náprava pro tento útok například neumožňuje, aby IP adresa, která generuje tento útok, používala [seznam ACL sítě](../virtual-network/virtual-networks-acl.md) nebo pravidlo [skupiny zabezpečení sítě](../virtual-network/security-overview.md#security-rules) . Pokud chcete získat další informace o různých typech výstrah, přečtěte si [typy výstrah zabezpečení](security-center-alerts-overview.md#security-alert-types).

> [!NOTE]
> Ve službě Security Center byla vydána ve verzi Limited Preview nová sada detekcí, které využívají záznamy auditu, což je běžný auditní rámec, k detekci škodlivého chování na počítačích s Linuxem. Pokud se chcete připojit k verzi Preview, zašlete [nám](mailto:ASC_linuxdetections@microsoft.com) e-mail s ID vašich předplatných.


## <a name="see-also"></a>Viz také:
V tomto dokumentu jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Řešení bezpečnostních incidentů v Azure Security Center](security-center-incident.md)
* [Funkce detekce ve službě Azure Security Center](security-center-detection-capabilities.md)
* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md)
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
