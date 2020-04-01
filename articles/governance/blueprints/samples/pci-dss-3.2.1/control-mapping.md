---
title: Ovládací prvky ukázkového plánu PCI-DSS v3.2.1
description: Řízení mapování platební karty průmyslu zabezpečení dat standard v3.2.1 podrobný plán na Zásady Azure a RBAC.
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: db21ac9d628e777b6ff2cc86516cfb1497f5a62f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76905633"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapování ovládacího prvku ukázky podrobného plánu PCI-DSS v3.2.1

V následujícím článku je podrobně popsáno, jak azure plány PCI-DSS v3.2.1 podrobný plán ukázka mapy pci-DSS v3.2.1 ovládací prvky. Další informace o ovládacích prvcích naleznete v tématu [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Následující mapování jsou na **PCI-DSS v3.2.1:2018** ovládací prvky. Pomocí navigace vpravo přejděte přímo na konkrétní mapování ovládacího prvku. Mnoho mapovaných ovládacích prvků se implementuje pomocí iniciativy [Zásad azure.](../../../policy/overview.md) Pokud chcete zkontrolovat celou iniciativu, otevřete **zásady** na webu Azure portal a vyberte stránku **Definice.** Potom najděte a vyberte ** \[ovládací prvky PCI auditu náhledu\] v3.2.1:2018 a nasaďte konkrétní rozšíření virtuálních počítačů pro podporu předdefinovaných** iniciativ zásad požadavků na audit.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definic [zásad Azure.](../../../policy/overview.md) Tyto zásady vám mohou pomoci [posoudit dodržování](../../../policy/how-to/get-compliance-data.md) ovládacího prvku; však často není 1:1 nebo úplnou shodu mezi ovládacím prvkem a jednu nebo více zásad. Jako **takový, kompatibilní** v zásadách Azure odkazuje pouze na zásady samotné; Tím nezajistíte, že jste plně kompatibilní se všemi požadavky ovládacího prvku. Kromě toho standard dodržování předpisů zahrnuje ovládací prvky, které nejsou v tuto chvíli adresovány žádnými definicemi zásad Azure. Dodržování předpisů v zásadách Azure je tedy pouze částečným zobrazením celkového stavu dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi zásad Azure pro tento ukázkový plán dodržování předpisů se může v průběhu času měnit. Chcete-li zobrazit historii změn, podívejte se na [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 a 1.3.4 Ochrana hranic

Tento podrobný plán vám pomůže spravovat a řídit sítě přiřazením definic [zásad Azure,](../../../policy/overview.md) které monitorují skupiny zabezpečení sítě s tolerantními pravidly. Pravidla, která jsou příliš tolerantní, mohou umožnit nezamýšlený přístup k síti a měla by být přezkoumána. Tento podrobný plán přiřazuje jednu definici zásad Azure, která monitoruje nechráněné koncové body, aplikace a účty úložiště. Koncové body a aplikace, které nejsou chráněny bránou firewall, a účty úložiště s neomezeným přístupem mohou umožnit nezamýšlený přístup k informacím obsaženým v informačním systému.

- Auditovat neomezený přístup k účtům úložiště v síti
- Přístup přes koncový bod směřující k Internetu by měl být omezen

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h a 6.5.3 Kryptografická ochrana

Tento podrobný plán vám pomůže vynutit zásady s použitím ovládacích prvků kryptografu přiřazením definic [zásad Azure,](../../../policy/overview.md) které vynucují specifické ovládací prvky kryptografu a auditují použití slabých kryptografických nastavení. Pochopení, kde vaše prostředky Azure může mít neoptimální kryptografické konfigurace vám může pomoci provést nápravná opatření k zajištění prostředků jsou nakonfigurovány v souladu s vaší zásady zabezpečení informací. Konkrétně zásady přiřazené tímto podrobným plánem vyžadují transparentní šifrování dat v databázích SQL; audituje chybějící šifrování na účtech úložiště a proměnné účtu automatizace. Existují také zásady, které řeší auditnení nezabezpečená připojení k účtům úložiště, aplikacím funkcí, aplikacím WebApp, aplikacím API a mezipaměti Redis a auditune nešifrovanou komunikaci Service Fabric.

- Aplikace function by měla být přístupná pouze přes protokol HTTPS
- Webová aplikace by měla být přístupná pouze přes protokol HTTPS.
- Aplikace API by měla být přístupná pouze přes protokol HTTPS
- Transparentní šifrování dat v databázích SQL by mělo být povoleno
- Šifrování disku by mělo být použito na virtuálních počítačích.
- Proměnné účtu automatizace by měly být šifrovány
- Měla by být povolena pouze zabezpečená připojení k mezipaměti Redis.
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Clustery Service Fabric by měly mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.
- Transparentní šifrování dat v databázích SQL by mělo být povoleno
- Nasazení transparentního šifrování dat SQL DB

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 a 11.2.1 Prohledávání chyb zabezpečení a aktualizace systému

Tento podrobný plán vám pomůže spravovat slabá místa informačního systému přiřazením definic [zásad Azure,](../../../policy/overview.md) které monitorují chybějící aktualizace systému, chyby zabezpečení operačního systému, chyby zabezpečení SQL a chyby zabezpečení virtuálních strojů v Centru zabezpečení Azure. Azure Security Center poskytuje funkce pro vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase.

- Sledování chybějící ochrany koncového bodu v Azure Security Center
- Nasazení výchozího rozšíření Microsoft IaaSAntimalware pro Windows Server
- Nasazení zjišťování hrozeb na serverech SQL
- Na počítače by se měly nainstalovat aktualizace systému
- Chyby zabezpečení v konfiguraci zabezpečení na vašich počítačích by měly být opraveny
- Chyby zabezpečení v databázích SQL by měly být opraveny
- Chyby zabezpečení by měly být opraveny řešením pro posouzení zranitelnosti

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 a 7.1.3 Oddělení cel

Mít jenom jednoho vlastníka předplatného Azure neumožňuje redundanci správy. Naopak s příliš mnoho vlastníků předplatného Azure můžete zvýšit potenciál pro porušení prostřednictvím ohroženého účtu vlastníka. Tento podrobný plán vám pomůže udržovat odpovídající počet vlastníků předplatného Azure přiřazením definic [zásad Azure,](../../../policy/overview.md) které auditují počet vlastníků pro předplatná Azure. Správa oprávnění vlastníka předplatného vám může pomoci implementovat vhodné oddělení povinností.

- K vašemu předplatnému by mělo být přiřazeno více než jednoho vlastníka.
- Pro vaše předplatné by měli být určeni maximálně 3 vlastníci. 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a a 8.3.1.b Správa privilegovaných přístupových práv

Tento podrobný plán vám pomůže omezit a řídit privilegovaná přístupová práva přiřazením definic [zásad Azure](../../../policy/overview.md) k auditování externích účtů s vlastníky, oprávněními k zápisu nebo čtení a účty zaměstnanců s oprávněními vlastníka nebo zápisu, které nemají povolené vícefaktorové ověřování. Azure implementuje řízení přístupu na základě rolí (RBAC) ke správě, kdo má přístup k prostředkům Azure. Pochopení, kde jsou implementována vlastní pravidla RBAC, vám může pomoci ověřit potřebu a správnou implementaci, protože vlastní pravidla RBAC jsou náchylná k chybám. Tento podrobný plán také přiřazuje definice [zásad Azure](../../../policy/overview.md) auditování použití ověřování Azure Active Directory pro servery SQL. Použití ověřování azure active directory zjednodušuje správu oprávnění a centralizuje správu identit uživatelů databází a dalších Microsoftů.  
services.
 
- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.
- Z předplatného by měly být odebrány externí účty s oprávněními k zápisu.
- Z předplatného by měly být odebrány externí účty s oprávněním ke čtení.
- Vícefaktorové informace by měly být povoleny u účtů s oprávněními vlastníka k vašemu předplatnému.
- MFA by měly mít povolené účty s oprávněními k zápisu ve vašem předplatném.
- Vícefaktorové informace by měly být povoleny u účtů s oprávněními ke čtení v rámci vašeho předplatného.
- Správce služby Azure Active Directory by měl být zřízen pro servery SQL.
- Auditování použití vlastních pravidel RBAC

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 a 8.1.5 Nejmenší oprávnění a kontrola přístupových práv uživatelů

Azure implementuje řízení přístupu na základě rolí (RBAC), které vám pomůže spravovat, kdo má přístup k prostředkům v Azure. Pomocí portálu Azure můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřazuje definice [zásad Azure](../../../policy/overview.md) k účtům auditování, které by měly být upřednostněny pro kontrolu, včetně odpisovaných účtů a externích účtů se zvýšenými oprávněními.

- Zastaralé účty by měly být odebrány z vašeho předplatného
- Zastaralé účty s oprávněními vlastníka by měly být odebrány z vašeho předplatného
- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.
- Z předplatného by měly být odebrány externí účty s oprávněními k zápisu.
- Z předplatného by měly být odebrány externí účty s oprávněním ke čtení.

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Odebrání nebo úprava přístupových práv

Azure implementuje řízení přístupu na základě rolí (RBAC), které vám pomohou spravovat, kdo má přístup k prostředkům v Azure. Pomocí Služby Azure Active Directory a RBAC můžete aktualizovat role uživatelů tak, aby odrážely změny v organizaci. V případě potřeby může být účty blokovány z přihlášení (nebo odebrat), což okamžitě odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřazuje definice [zásad Azure](../../../policy/overview.md) auditovanému účtu, který by měl být považován za odebrání.

- Zastaralé účty by měly být odebrány z vašeho předplatného
- Zastaralé účty s oprávněními vlastníka by měly být odebrány z vašeho předplatného

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b a 8.2.5 Ověřování na základě hesla

Tento podrobný plán pomáhá vynutit silná hesla přiřazením definic [zásad Azure,](../../../policy/overview.md) které auditují virtuální počítače s Windows, které nevynucují minimální sílu a další požadavky na hesla. Povědomí o virtuálních počítačů v rozporu se zásadami pevnosti hesla vám pomůže provést nápravná opatření k zajištění hesla pro všechny uživatelské účty virtuálních počítačů jsou v souladu se zásadami.

- \[Náhled\]: Auditovat virtuální počítače se systémem Windows, které nemají maximální stáří hesla 70 dnů
- \[Náhled\]: Nasazení požadavků na auditování virtuálních počítače se systémem Windows, které nemají maximální stáří hesla 70 dní
- \[Náhled\]: Auditovat virtuální počítače se systémem Windows, které neomezují minimální délku hesla na 14 znaků
- \[Náhled\]: Nasazení požadavků na auditování virtuálních počítače se systémem Windows, které neomezují minimální délku hesla na 14 znaků
- \[Náhled\]: Auditování virtuálních počítače se systémem Windows, které umožňují opakované použití předchozích 24 hesel
- \[Náhled\]: Nasazení požadavků na auditování virtuálních počítače se systémem Windows, které umožňují opakované použití předchozích 24 hesel

## <a name="103-and-1054-audit-generation"></a>10.3 a 10.5.4 Generování auditu

Tento podrobný plán vám pomůže zajistit, aby se systémové události zaznamenávalo přiřazením definic [zásad Azure,](../../../policy/overview.md) které auditují nastavení protokolu prostředků Azure.
Diagnostické protokoly poskytují přehled o operacích, které byly provedeny v rámci prostředků Azure. Protokoly Azure spoléhají na synchronizované interní hodiny k vytvoření časově korelovaného záznamu událostí napříč prostředky.

- Auditování by mělo být povoleno v rozšířeném nastavení zabezpečení dat na serveru SQL Server.
- Auditování nastavení diagnostiky
- Auditování nastavení auditování na úrovni serveru SQL
- Nasazení auditování na serverech SQL
- Účty úložiště by se měly migrovat do nových prostředků Azure Resource Manageru.
- Virtuální počítače by se měly migrovat do nových prostředků Azure Resource Manageru.

## <a name="1236-and-1237-information-security"></a>12.3.6 a 12.3.7 Bezpečnost informací

Tento podrobný plán vám pomůže spravovat a řídit vaši síť přiřazením definic [zásad Azure,](../../../policy/overview.md) které auditují přijatelná umístění v síti a schválené produkty společnosti povolené pro životní prostředí. Ty jsou přizpůsobitelné každou společností prostřednictvím parametrů zásad v rámci každé z těchto zásad.

- Povolená umístění
- Povolená umístění pro skupiny prostředků

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacích prvku plánu PCI-DSS v3.2.1, navštivte následující články, kde se dozvíte o přehledu a o tom, jak nasadit tuto ukázku:

> [!div class="nextstepaction"]
> [Plán PCI-DSS v3.2.1 – přehled](./index.md)
> [podrobný plán PCI-DSS v3.2.1 – Nasazení kroků](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).