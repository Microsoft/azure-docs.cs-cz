---
title: UK OFFICIAL & UK NHS blueprint vzorové kontroly
description: Kontrolní mapování uk oficiální a UK NHS plán vzorků. Každý ovládací prvek je mapován na jednu nebo více zásad Azure, které pomáhají s hodnocením.
ms.date: 12/04/2019
ms.topic: sample
ms.openlocfilehash: 5bef590013a9ef06b791e58dc6c82e74dffe1a17
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74851362"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Kontrolní mapování uk oficiální a uk NHS plán vzorků

Následující článek podrobně popisuje, jak uk OFICIÁLNÍ a UK NHS plán vzorky mapovat na UK OFFICIAL a UK NHS kontroly. Další informace o ovládacích prvcích naleznete [v tématu UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications).

Následující mapování jsou pro **britské oficiální** a britské **nhs** kontroly. Pomocí navigace vpravo přejděte přímo na konkrétní mapování ovládacího prvku. Mnoho mapovaných ovládacích prvků se implementuje pomocí iniciativy [Zásad azure.](../../../policy/overview.md) Pokud chcete zkontrolovat celou iniciativu, otevřete **zásady** na webu Azure portal a vyberte stránku **Definice.** Potom najděte a vyberte ** \[ovládací prvky Preview\] Audit UK OFFICIAL a UK NHS a nasaďte konkrétní rozšíření virtuálních her, abyste podpořili předdefinovanou** iniciativu zásad na audit.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definic [zásad Azure.](../../../policy/overview.md) Tyto zásady vám mohou pomoci [posoudit dodržování](../../../policy/how-to/get-compliance-data.md) ovládacího prvku; však často není 1:1 nebo úplnou shodu mezi ovládacím prvkem a jednu nebo více zásad. Jako **takový, kompatibilní** v zásadách Azure odkazuje pouze na zásady samotné; Tím nezajistíte, že jste plně kompatibilní se všemi požadavky ovládacího prvku. Kromě toho standard dodržování předpisů zahrnuje ovládací prvky, které nejsou v tuto chvíli adresovány žádnými definicemi zásad Azure. Dodržování předpisů v zásadách Azure je tedy pouze částečným zobrazením celkového stavu dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi zásad Azure pro tento ukázkový plán dodržování předpisů se může v průběhu času měnit. Chcete-li zobrazit historii změn, podívejte se na [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 Údaje v oblasti ochrany při přepravě

Podrobný plán vám pomůže zajistit zabezpečení přenosu informací pomocí služeb Azure přiřazením definic [zásad Azure,](../../../policy/overview.md) které auditují nezabezpečené připojení k účtům úložiště a mezipaměti Redis.

- Měla by být povolena pouze zabezpečená připojení k mezipaměti Redis.
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Zobrazit výsledky auditu z webových serverů windows, které nepoužívají zabezpečené komunikační protokoly
- Nasazení požadavků pro auditování webových serverů systému Windows, které nepoužívají zabezpečené komunikační protokoly
- Nejnovější verze TLS by měla být použita ve vaší aplikaci API
- Ve webové aplikaci by měla být použita nejnovější verze TLS.
- Nejnovější verze TLS by měla být použita ve vaší funkční aplikaci

## <a name="23-data-at-rest-protection"></a>2.3 Ochrana údajů v klidovém stavu

Tento podrobný plán vám pomůže vynutit zásady používání ovládacích prvků kryptografu přiřazením definic [zásad Azure,](../../../policy/overview.md) které vynucují specifické ovládací prvky kryptografu a auditují použití slabých kryptografických nastavení.
Pochopení, kde vaše prostředky Azure může mít neoptimální kryptografické konfigurace vám může pomoci provést nápravná opatření k zajištění prostředků jsou nakonfigurovány v souladu s vaší zásady zabezpečení informací. Konkrétně zásady přiřazené tímto plánem vyžadují šifrování pro účty úložiště datových jezer; vyžadovat transparentní šifrování dat v databázích SQL; auditovat chybějící šifrování na účtech úložiště, databázích SQL, discích virtuálních počítačů a proměnných účtů automatizace; audituje nezabezpečené připojení k účtům úložiště a mezipaměti Redis; audit slabého šifrování hesel virtuálního počítače; a auditujte nešifrovanou komunikaci Service Fabric.

- Šifrování disku by mělo být použito na virtuálních počítačích.
- Proměnné účtu automatizace by měly být šifrovány
- Clustery Service Fabric by měly mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.
- Transparentní šifrování dat v databázích SQL by mělo být povoleno
- Nasazení transparentního šifrování dat SQL DB
- Vyžadovat šifrování na účtech Data Lake Store
- Povolená umístění (byla pevně zakódována na "UK SOUTH" a "UK WEST")
- Povolená umístění pro skupiny prostředků (byla pevně zakódována na "UK SOUTH" a "UK WEST")

## <a name="52-vulnerability-management"></a>5.2 Správa chyb zabezpečení

Tento podrobný plán vám pomůže spravovat chyby zabezpečení informačního systému přiřazením definic [zásad Azure,](../../../policy/overview.md) které monitorují chybějící ochranu koncových bodů, chybějící aktualizace systému, chyby zabezpečení operačního systému, chyby zabezpečení SQL a chyby zabezpečení virtuálních strojů. Tyto přehledy poskytují informace o stavu zabezpečení nasazených prostředků v reálném čase a mohou vám pomoci určit prioritu nápravných akcí.

- Sledování chybějící ochrany koncového bodu v Azure Security Center
- Na počítače by se měly nainstalovat aktualizace systému
- Aktualizace systému na škálovacísady virtuálních strojů by měly být nainstalovány
- Chyby zabezpečení v konfiguraci zabezpečení na vašich počítačích by měly být opraveny
- Chyby zabezpečení v databázích SQL by měly být opraveny
- Chyby zabezpečení by měly být opraveny řešením pro posouzení zranitelnosti
- Posouzení ohrožení zabezpečení by mělo být povoleno na serverech SQL
- Posouzení ohrožení zabezpečení by mělo být povoleno ve vašich instancích spravovaných SQL.
- Chyby zabezpečení v konfiguraci zabezpečení ve škálovacích sadách virtuálních počítačů by měly být opraveny.
- Rozšířené zabezpečení dat by mělo být povoleno ve vašich instancích spravovaných SQL.
- Na serverech SQL by mělo být povoleno rozšířené zabezpečení dat.

## <a name="53-protective-monitoring"></a>5.3 Ochranné monitorování

Tento podrobný plán pomáhá chránit prostředky informačního systému přiřazením definic [zásad Azure,](../../../policy/overview.md) které poskytují ochranné monitorování neomezeného přístupu, povolit aktivitu seznamu a hrozby.

- Auditovat neomezený přístup k účtům úložiště v síti
- Adaptivní ovládací prvky aplikací by měly být povoleny na virtuálních počítačích
- Auditování virtuálních počítačů bez nakonfigurovaného zotavení po havárii
- DDoS Protection Standard by měla být povolena
- Rozšířené typy ochrany před internetovými hrozbami by měly být nastaveny na hodnotu "Vše" v nastavení rozšířeného zabezpečení dat spravované ho SQL.
- Rozšířené typy ochrany před hrozbami by měly být v nastavení rozšířeného zabezpečení dat serveru SQL nastaveny na hodnotu Vše.
- Nasazení zjišťování hrozeb na serverech SQL
- Nasazení výchozího rozšíření Microsoft IaaSAntimalware pro Windows Server

## <a name="9-secure-user-management"></a>9 Bezpečná správa uživatelů 

Azure implementuje řízení přístupu na základě rolí (RBAC), které vám pomůže spravovat, kdo má přístup k prostředkům v Azure. Pomocí portálu Azure můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán vám pomůže omezit a řídit přístupová práva přiřazením definic [zásad Azure](../../../policy/overview.md) k auditování externích účtů s oprávněními vlastníka a/nebo oprávněními pro čtení a zápis s vlastníky, oprávněními pro čtení a zápis, která nemají povolené vícefaktorové ověřování.

- Vícefaktorové informace by měly být povoleny u účtů s oprávněními vlastníka k vašemu předplatnému.
- MFA by měly mít povolené účty s oprávněními k zápisu ve vašem předplatném.
- Vícefaktorové informace by měly být povoleny u účtů s oprávněními ke čtení v rámci vašeho předplatného.
- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.
- Z předplatného by měly být odebrány externí účty s oprávněními k zápisu.
- Z předplatného by měly být odebrány externí účty s oprávněním ke čtení.

## <a name="10-identity-and-authentication"></a>10 Identita a ověřování

Tento podrobný plán vám pomůže omezit a řídit přístupová práva přiřazením definic [zásad Azure](../../../policy/overview.md) k auditování externích účtů s oprávněními vlastníka a/nebo oprávněními pro čtení a zápis s vlastníky, oprávněními pro čtení a zápis, která nemají povolené vícefaktorové ověřování.

- Vícefaktorové informace by měly být povoleny u účtů s oprávněními vlastníka k vašemu předplatnému.
- MFA by měly mít povolené účty s oprávněními k zápisu ve vašem předplatném.
- Vícefaktorové informace by měly být povoleny u účtů s oprávněními ke čtení v rámci vašeho předplatného.
- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.
- Z předplatného by měly být odebrány externí účty s oprávněními k zápisu.
- Z předplatného by měly být odebrány externí účty s oprávněním ke čtení.

Tento podrobný plán přiřazuje definice zásad Azure auditování použití ověřování Azure Active Directory pro servery SQL a Service Fabric. Použití ověřování Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databází a dalších služeb Microsoftu.

- Správce služby Azure Active Directory by měl být zřízen pro servery SQL.
- Clustery Service Fabric by měly používat azure active directory jenom pro ověřování klientů.

Tento podrobný plán také přiřazuje definice zásad Azure k účtům auditu, které by měly být upřednostněny pro kontrolu, včetně odpisovaných účtů a externích účtů. V případě potřeby může být účty blokovány z přihlášení (nebo odebrat), což okamžitě odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřadí dvě definice zásad Azure auditovat odpisovaný účet, který by měl být považován za odebrání.

- Zastaralé účty by měly být odebrány z vašeho předplatného
- Zastaralé účty s oprávněními vlastníka by měly být odebrány z vašeho předplatného
- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.
- Z předplatného by měly být odebrány externí účty s oprávněními k zápisu.

Tento podrobný plán také přiřadí definici zásad Azure, která audituje oprávnění k souboru hesla virtuálního počítače linuxu, aby se upozorňovala, pokud jsou nastavena nesprávně. Tento návrh umožňuje provést nápravná opatření k zajištění ověření nejsou ohroženy.

- \[Náhled\]: Zobrazit výsledky auditu z virtuálních počítačů s Linuxem, které nemají oprávnění k souboru passwd nastavená na 0644

Tento podrobný plán pomáhá vynutit silná hesla přiřazením definic zásad Azure, které auditují virtuální počítače s Windows, které nevynucují minimální sílu a další požadavky na hesla. Povědomí o virtuálních počítačů v rozporu se zásadami pevnosti hesla vám pomůže provést nápravná opatření k zajištění hesla pro všechny uživatelské účty virtuálních počítačů jsou v souladu se zásadami.

- \[Náhled\]: Nasazení předpokladů pro auditování virtuálních počítače se systémem Windows, u kterých není povoleno nastavení složitosti hesla
- \[Náhled\]: Nasazení předpokladů pro auditování virtuálních počítače se systémem Windows, které nemají maximální stáří hesla 70 dní
- \[Náhled\]: Nasazení předpokladů pro auditování virtuálních počítače se systémem Windows, které nemají minimální stáří hesla 1 den
- \[Náhled\]: Nasazení předpokladů pro auditování virtuálních aplikací windows, které neomezují minimální délku hesla na 14 znaků
- \[Náhled\]: Nasazení předpokladů pro auditování virtuálních aplikací Windows, které umožňují opakované použití předchozích 24 hesel
- \[Náhled\]: Zobrazení výsledků auditu z virtuálních počítače se systémem Windows, u kterých není povoleno nastavení složitosti hesla
- \[Náhled\]: Zobrazit výsledky auditu z virtuálních počítače se systémem Windows, které nemají maximální stáří hesla 70 dnů
- \[Náhled\]: Zobrazit výsledky auditu z virtuálních počítače se systémem Windows, které nemají minimální stáří hesla 1 den
- \[Náhled\]: Zobrazit výsledky auditu z virtuálních počítače se systémem Windows, které neomezují minimální délku hesla na 14 znaků
- \[Náhled\]: Zobrazení výsledků auditu z virtuálních počítače se systémem Windows, které umožňují opakované použití předchozích 24 hesel

Tento podrobný plán vám také pomůže řídit přístup k prostředkům Azure přiřazením definic zásad Azure. Tyto zásady audit použití typů prostředků a konfigurace, které mohou umožnit více tolerantní přístup k prostředkům. Principy prostředků, které jsou v rozporu s těmito zásadami vám může pomoci přijmout nápravná opatření k zajištění přístupu prostředků Azure je omezena na oprávněné uživatele.

- \[Preview\]: Nasazení požadavků na auditování virtuálních počítačů s Linuxem, které mají účty bez hesel
- \[Preview\]: Nasazení požadavků na auditování virtuálních počítačů s Linuxem, které umožňují vzdálená připojení z účtů bez hesel
- \[Preview\]: Auditování virtuálních počítačů s Linuxem, které mají účty bez hesel
- \[Preview\]: Auditování virtuálních počítačů s Linuxem, které umožňují vzdálená připojení z účtů bez hesel
- Účty úložiště by se měly migrovat do nových prostředků Azure Resource Manageru.
- Virtuální počítače by se měly migrovat do nových prostředků Azure Resource Manageru.
- Auditování virtuálních počítačů, které nepoužívají spravované disky

## <a name="11-external-interface-protection"></a>11 Ochrana externího rozhraní

Kromě použití více než 25 zásad pro odpovídající zabezpečenou správu uživatelů vám tento podrobný plán pomáhá chránit rozhraní služeb před neoprávněným přístupem přiřazením definice [zásad Azure,](../../../policy/overview.md) která monitoruje účty neomezeného úložiště. Účty úložiště s neomezeným přístupem mohou umožnit nezamýšlený přístup k informacím obsaženým v informačním systému. Tento podrobný plán také přiřadí zásadu, která umožňuje adaptivní ovládací prvky aplikací na virtuálních počítačích.

- Auditovat neomezený přístup k účtům úložiště v síti
- Adaptivní ovládací prvky aplikací by měly být povoleny na virtuálních počítačích
- Pravidla nsg pro webové aplikace na IaaS by měla být posílena
- Přístup přes koncový bod směřující k Internetu by měl být omezen
- Pravidla skupiny zabezpečení sítě pro virtuální počítače orientované na Internet by měla být posílena
- Řešení ochrany koncových bodů by mělo být nainstalováno na škálovacích sadách virtuálních strojů.
- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby
- Auditovat neomezený přístup k účtům úložiště v síti
- Vzdálené ladění by mělo být vypnuto pro aplikaci Function App
- Vzdálené ladění by mělo být vypnuto pro webovou aplikaci.
- Vzdálené ladění by mělo být vypnuto pro aplikaci API.
- Webová aplikace by měla být přístupná pouze přes protokol HTTPS.
- Aplikace function by měla být přístupná pouze přes protokol HTTPS
- Aplikace API by měla být přístupná pouze přes protokol HTTPS

## <a name="12-secure-service-administration"></a>12 Správa zabezpečené služby

Azure implementuje řízení přístupu na základě rolí (RBAC), které vám pomůže spravovat, kdo má přístup k prostředkům v Azure. Pomocí portálu Azure můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán vám pomůže omezit a řídit privilegovaná přístupová práva přiřazením pěti definic [zásad Azure](../../../policy/overview.md) k auditování externích účtů s oprávněními vlastníka nebo zápisu a účty s vlastníkem a oprávněními k zápisu, která nemají povolené vícefaktorové ověřování.

Systémy používané pro správu cloudové služby budou mít k této službě vysoce privilegovaný přístup. Jejich kompromis by měl významný dopad, včetně prostředků k obejití bezpečnostních kontrol a krádeže nebo manipulaci s velkými objemy dat. Metody používané administrátory služeb pro správu provozní služby by měly být navrženy tak, aby se zmírnilo jakékoli riziko zneužití, které by mohlo ohrozit bezpečnost služby. Pokud tento princip není implementován, útočník může mít prostředky k obejití ovládacích prvků zabezpečení a ukrást nebo manipulovat s velkými objemy dat.

- Vícefaktorové informace by měly být povoleny u účtů s oprávněními vlastníka k vašemu předplatnému.
- MFA by měly mít povolené účty s oprávněními k zápisu ve vašem předplatném.
- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.
- Z předplatného by měly být odebrány externí účty s oprávněními k zápisu.

Tento podrobný plán přiřazuje definice zásad Azure auditování použití ověřování Azure Active Directory pro servery SQL a Service Fabric. Použití ověřování Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databází a dalších služeb Microsoftu.

- Správce služby Azure Active Directory by měl být zřízen pro servery SQL.
- Clustery Service Fabric by měly používat azure active directory jenom pro ověřování klientů.

Tento podrobný plán také přiřazuje definice zásad Azure k účtům auditování, které by měly být upřednostněny pro kontrolu, včetně odpisovaných účtů a externích účtů se zvýšenými oprávněními. V případě potřeby může být účty blokovány z přihlášení (nebo odebrat), což okamžitě odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřadí dvě definice zásad Azure auditovat odpisovaný účet, který by měl být považován za odebrání.

- Zastaralé účty by měly být odebrány z vašeho předplatného
- Zastaralé účty s oprávněními vlastníka by měly být odebrány z vašeho předplatného
- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.
- Z předplatného by měly být odebrány externí účty s oprávněními k zápisu.

Tento podrobný plán také přiřadí definici zásad Azure, která audituje oprávnění k souboru hesla virtuálního počítače linuxu, aby se upozorňovala, pokud jsou nastavena nesprávně. Tento návrh umožňuje provést nápravná opatření k zajištění ověření nejsou ohroženy.

- \[Náhled\]: Audit linuxového virtuálního počítače /etc/passwd oprávnění souboru jsou nastaveny na 0644

## <a name="13-audit-information-for-users"></a>13 Informace o auditu pro uživatele

Tento podrobný plán vám pomůže zajistit, aby se systémové události zaznamenávalo přiřazením definic [zásad Azure,](../../../policy/overview.md) které auditují nastavení protokolu prostředků Azure. Přiřazená zásada také audituje, pokud virtuální počítače neposílají protokoly do zadaného pracovního prostoru analýzy protokolů.

- Auditování by mělo být povoleno v rozšířeném nastavení zabezpečení dat na serveru SQL Server.
- Auditování nastavení diagnostiky
- \[Náhled:\]Nasazení agenta analýzy protokolů pro virtuální počítače s Linuxem
- \[Náhled\]: Nasazení agenta analýzy protokolů pro virtuální servery Windows
- Nasazení sledovacího procesu sítě při vytváření virtuálních sítí

## <a name="next-steps"></a>Další kroky

Nyní, když jste zkontrolovali mapování řízení plánů NHS VE Velké Británii a Velké Británii, navštivte následující články, kde se dozvíte o přehledu a o tom, jak nasadit tuto ukázku:

> [!div class="nextstepaction"]
> [Uk OFFICIAL a UK NHS plány - Přehled](./index.md)
> [UK OFFICIAL a UK NHS plány - Nasazení kroků](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).
