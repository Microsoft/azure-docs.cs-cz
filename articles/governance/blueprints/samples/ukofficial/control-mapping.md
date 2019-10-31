---
title: '& Oficiální Velká Británie – ukázky NHS Details – mapování řízení'
description: Mapování ovládacího prvku OFICIÁLNÍch a NHS podrobných plánů pro Spojené království Každý ovládací prvek je namapován na jednu nebo více zásad Azure, které pomáhají s posouzením.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: e86c7040e94553b032b565e282639047a1ed8750
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162960"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Mapování ovládacího prvku OFICIÁLNÍch a NHS podrobných plánů pro Spojené království

Následující článek podrobně popisuje, jak jsou vzorky ÚŘEDNÍch a NHS podrobných plánů pro Spojené království mapovány na oficiální a britský NHS ovládací prvky. Další informace o ovládacích prvcích najdete v článku [úředník pro Spojené království](https://www.gov.uk/government/publications/government-security-classifications).

Následující mapování jsou pro **oficiální** a britský ovládací prvky **NHS** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte **\[Preview\] auditovat oficiální a UK NHS ovládací prvky a nasaďte specifická rozšíření virtuálních počítačů, která budou podporovat požadavky na** integrovanou iniciativu zásad pro audit.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 data v přenosové ochraně

Podrobný plán vám pomůže zajistit, aby přenos informací se službami Azure byl zabezpečený tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které auditují nezabezpečená připojení k účtům úložiště a Redis Cache.

- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.
- Zabezpečený přenos do účtů úložiště by měl být povolený.

## <a name="23-data-at-rest-protection"></a>2,3 dat při ochraně proti REST

Tento podrobný plán vám pomůže vyhovět zásadám používání ovládacích prvků cryptograph přiřazením definicí [Azure Policy](../../../policy/overview.md) , které vysazují konkrétní ovládací prvky cryptograph a auditují použití slabého kryptografického nastavení.
Porozumět tomu, kde vaše prostředky Azure můžou mít neoptimální Kryptografické konfigurace, můžou podniknout nápravné akce, které zajistí konfiguraci prostředků v souladu s vašimi zásadami zabezpečení informací. Konkrétně zásady přiřazené tímto plánem vyžadují šifrování pro účty Data Lake Storage. vyžadovat transparentní šifrování dat na databázích SQL; Auditovat chybějící šifrování u účtů úložiště, databází SQL, disků virtuálních počítačů a proměnných účtu Automation; auditujte nezabezpečená připojení k účtům úložiště a Redis Cache; Auditovat slabé šifrování hesla virtuálního počítače; a auditujte nešifrované Service Fabric komunikaci.

- Je třeba povolit transparentní šifrování dat databází SQL.
- Na virtuálních počítačích by se mělo použít šifrování disku
- Proměnné účtu Automation by se měly šifrovat.
- Zabezpečený přenos do účtů úložiště by měl být povolený.
- Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.
- Je třeba povolit transparentní šifrování dat databází SQL.
- Nasazení transparentního šifrování dat databáze SQL
- Vyžadovat šifrování u Data Lake Store účtů
- Povolená umístění (byla pevně zakódována na Velká Británie – jih a Velká Británie – západ)
- Povolená umístění pro skupiny prostředků (bylo pevně zakódováno na Velká Británie – jih a Velká Británie – západ)

## <a name="52-vulnerability-management"></a>Správa ohrožení zabezpečení 5,2

Tento podrobný plán vám pomůže spravovat chyby zabezpečení systému pomocí přiřazení [Azure Policych](../../../policy/overview.md) definic, které monitorují chybějící službu Endpoint Protection, chybějící aktualizace systému, ohrožení zabezpečení operačního systému, chyby zabezpečení SQL a virtuální. ohrožení zabezpečení počítače. Tyto přehledy poskytují informace o stavu zabezpečení nasazených prostředků v reálném čase a můžou vám pomůžou určit prioritu nápravných akcí.

- Monitorovat chybějící Endpoint Protection v Azure Security Center
- Do vašich počítačů by se měly nainstalovat aktualizace systému
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="53-protective-monitoring"></a>Monitorování ochrany 5,3

Tento podrobný plán vám pomůže chránit assety informačních systémů tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které poskytují ochranu před neomezeným přístupem, aktivitou seznamu povolených a hrozbami.

- Auditování neomezeného síťového přístupu k účtům úložiště
- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.
- Nasazení detekce hrozeb na SQL serverech
- Nasazení výchozích rozšíření Microsoft IaaS anti-malware pro Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 zabezpečená Správa uživatelů/10 Identita a ověřování

Azure implementuje řízení přístupu na základě role (RBAC), které vám pomůže se správou toho, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán vám pomůže omezit a řídit přístupová práva tím, že přiřadí definice [Azure Policy](../../../policy/overview.md) k auditování externích účtů s oprávněními vlastníka nebo čtení/zápisu a s účty s oprávněním vlastníka, čtení a zápisu, která nemají Multi-Factor. ověřování je povoleno.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.
- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z předplatného by se měly odebrat externí účty s oprávněními pro zápis
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.

Tento podrobný plán přiřadí Azure Policy definice pro auditování použití Azure Active Directory ověřování pro servery SQL a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.

Tento podrobný plán také přiřadí Azure Policy definice k auditu účtů, které by měly být pro kontrolu upřednostněny, včetně účtů s odepsáním a externích účtů. V případě potřeby se můžou účty zablokovat (nebo odebírat), což okamžitě odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřadí dvě Azure Policy definice k auditu účtu, který by měl být považován za odebrání.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z předplatného by se měly odebrat externí účty s oprávněními pro zápis

Tento podrobný plán také přiřadí definici Azure Policy, která Audituje oprávnění souboru s heslem pro virtuální počítače Linux, pokud jsou nesprávně nastavena. Tento návrh vám umožní podniknout nápravná opatření, aby nedošlo k ohrožení ověřovatelů.

- \[Preview\]: oprávnění k souboru auditu/etc/passwd virtuálního počítače pro Linux jsou nastavena na 0644.

Tento podrobný plán vám pomůže vymáhat silná hesla přiřazením Azure Policy definic, které auditují virtuální počítače s Windows, které nevyžadují minimální sílu a jiné požadavky na heslo. Dostupnost virtuálních počítačů v rozporu s zásadami síly hesla vám pomůže provést nápravné akce, které zajistí, že hesla pro všechny uživatelské účty virtuálních počítačů jsou kompatibilní se zásadami.

- \[Preview\]: nasaďte požadavky na auditování virtuálních počítačů s Windows, u kterých není povolené nastavení složitosti hesla.
- \[Preview\]: nasaďte požadavky na auditování virtuálních počítačů s Windows, které nemají maximální stáří hesla 70 dní.
- \[Preview\]: nasaďte požadavky na auditování virtuálních počítačů s Windows, které nemají minimální stáří hesla 1 den.
- \[Preview\]: nasaďte požadavky na auditování virtuálních počítačů s Windows, které neomezují minimální délku hesla na 14 znaků.
- \[Preview\]: nasaďte požadavky na audit virtuálních počítačů s Windows, které umožňují opakované použití předchozích 24 hesel.
- \[Preview\]: Auditovat virtuální počítače s Windows, u kterých není povolené nastavení složitosti hesla
- \[Preview\]: Auditovat virtuální počítače s Windows, které nemají maximální stáří hesla 70 dnů.
- \[Preview\]: Auditovat virtuální počítače s Windows, které nemají minimální stáří hesla 1 den.
- \[Preview\]: Auditovat virtuální počítače s Windows, které neomezují minimální délku hesla na 14 znaků
- \[Preview\]: Auditovat virtuální počítače s Windows, které umožňují opakované použití předchozích 24 hesel

Tento podrobný plán vám také pomůže řídit přístup k prostředkům Azure přiřazením Azure Policy definice. Tyto zásady auditují použití typů prostředků a konfigurací, které by mohly umožňovat více opravňující přístup k prostředkům. Principy prostředků, které jsou v rozporu s těmito zásadami, vám pomůžou podniknout nápravné akce, které zajistí, že přístup k prostředkům Azure bude omezený na autorizované uživatele.

- \[Preview\]: nasazení požadavků pro audit virtuálních počítačů se systémem Linux, které mají účty bez hesel
- \[Preview\]: nasazení požadavků pro auditování virtuálních počítačů se systémem Linux, které umožňují vzdálená připojení z účtů bez hesla
- \[Preview\]: Auditovat virtuální počítače Linux s účty bez hesel
- \[Preview\]: Auditovat virtuální počítače Linux, které umožňují vzdálená připojení z účtů bez hesel
- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.
- Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.
- Auditovat virtuální počítače, které nepoužívají spravované disky

## <a name="11-external-interface-protection"></a>11 ochrany externích rozhraní

Kromě používání více než 25 zásad pro příslušnou zabezpečenou správu uživatelů tento plán vám pomůže chránit rozhraní služeb před neoprávněným přístupem tím, že přiřazuje definici [Azure Policy](../../../policy/overview.md) , která monitoruje neomezený účet úložiště. Účty úložiště s neomezeným přístupem můžou umožňovat neúmyslný přístup k informacím obsaženým v informačním systému. Tento podrobný plán také přiřadí zásadu, která umožňuje Adaptivní řízení aplikací na virtuálních počítačích.

- Auditování neomezeného síťového přístupu k účtům úložiště
- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="12-secure-service-administration"></a>12. Správa zabezpečené služby

Azure implementuje řízení přístupu na základě role (RBAC), které vám pomůže se správou toho, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán vám pomůže omezit a řídit privilegovaná přístupová práva tím, že přiřazuje pět definic [Azure Policy](../../../policy/overview.md) k auditu externích účtů s oprávněním vlastníka nebo zápisu a s oprávněním k zápisu, která nemají. ověřování Multi-Factor Authentication je povolené.

Systémy používané pro správu cloudové služby budou mít vysoce privilegovaný přístup k této službě. Jejich ohrožení by mělo významný dopad, včetně prostředků pro obejít řízení zabezpečení a odcizit nebo manipulovat s velkými objemy dat. Metody používané správci poskytovatele služeb ke správě provozní služby by měly být navržené tak, aby zmírnily riziko využívání, které by mohlo ohrozit zabezpečení služby. Pokud tento princip není implementován, může útočník obejít ovládací prvky zabezpečení a ukrást nebo manipulovat s velkými objemy dat.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z předplatného by se měly odebrat externí účty s oprávněními pro zápis

Tento podrobný plán přiřadí Azure Policy definice pro auditování použití Azure Active Directory ověřování pro servery SQL a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.

Tento podrobný plán také přiřadí Azure Policy definice pro audit účtů, které by měly být nastavené na revizi, včetně odepsáných účtů a externích účtů se zvýšenými oprávněními. V případě potřeby se můžou účty zablokovat (nebo odebírat), což okamžitě odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřadí dvě Azure Policy definice k auditu účtu, který by měl být považován za odebrání.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z předplatného by se měly odebrat externí účty s oprávněními pro zápis

Tento podrobný plán také přiřadí definici Azure Policy, která Audituje oprávnění souboru s heslem pro virtuální počítače Linux, pokud jsou nesprávně nastavena. Tento návrh vám umožní podniknout nápravná opatření, aby nedošlo k ohrožení ověřovatelů.

- \[Preview\]: oprávnění k souboru auditu/etc/passwd virtuálního počítače pro Linux jsou nastavena na 0644.

## <a name="13-audit-information-for-users"></a>13 informací o auditu pro uživatele

Tento podrobný plán vám pomůže zajistit, aby byly systémové události zaznamenávány přiřazením [Azure Policy](../../../policy/overview.md) definicí, které auditují nastavení protokolů v prostředcích Azure. Přiřazená zásada taky Audituje, pokud virtuální počítače neodesílají protokoly do zadaného pracovního prostoru Log Analytics.

- Auditování by mělo být povolené pro pokročilá nastavení zabezpečení dat na SQL Server
- Auditování nastavení diagnostiky
- Auditovat nastavení auditování na úrovni SQL serveru
- \[Preview\]: nasazení Log Analytics agenta pro virtuální počítače se systémem Linux
- \[Preview\]: nasazení Log Analytics agenta pro virtuální počítače s Windows
- Při vytváření virtuálních sítí nasadit sledovací proces sítě

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacího prvku OFICIÁLNÍch a NHSch plánů UK, najdete v následujících článcích informace o přehledu a způsobu nasazení této ukázky:

> [!div class="nextstepaction"]
> [Oficiální a NHS modrotisky pro Spojené království – přehledy](./index.md)
> [UK oficiální a Velká Británie NHS modrotisky – postup nasazení](./deploy.md)

Další články o semodrotiskych a jejich použití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)