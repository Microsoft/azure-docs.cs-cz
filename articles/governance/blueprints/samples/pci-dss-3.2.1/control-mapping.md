---
title: Ovládací prvky ukázkového plánu PCI-DSS v 3.2.1
description: Mapování ovládacího prvku ukázka zabezpečení dat v odvětví platební karty Standard v 3.2.1 pro Azure Policy a Azure RBAC.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 9b1cf7702b627ae073f0172dde4694060004cb3f
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045151"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapování ovládacích prvků pro ukázka PCI-DSS v 3.2.1 podrobný plán

Následující článek podrobně popisuje, jak ukázka pro Azure modrotisky PCI-DSS v 3.2.1 podrobný plán mapuje na ovládací prvky PCI-DSS v 3.2.1. Další informace o ovládacích prvcích najdete v tématu [PCI-DSS v 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Následující mapování jsou pro ovládací prvky **PCI-DSS v 3.2.1:2018** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte rozhraní **PCI v 3.2.1:** předdefinovaná iniciativa zásad 2018.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. Nicméně často není jedna nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 a 1.3.4 hranice ochrany

Tento podrobný plán vám pomůže se správou a řízením sítí tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které monitorují skupiny zabezpečení sítě s povolující pravidla. Pravidla, která jsou příliš neoprávněná, mohou umožňovat neúmyslný přístup k síti a měly by být přezkoumány. Tento podrobný plán přiřadí jednu Azure Policy definice, které sledují nechráněné koncové body, aplikace a účty úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem, můžou dovolit neúmyslný přístup k informacím obsaženým v informačním systému.

- Auditování neomezeného síťového přístupu k účtům úložiště
- Přístup přes internetový koncový bod by měl být omezený.

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4. a, 4,1, 4.1. g, 4.1. h a 6.5.3 Cryptographic Protection

Tento podrobný plán vám pomůže vyhovět zásadám pomocí ovládacích prvků cryptograph přiřazením definicí [Azure Policy](../../../policy/overview.md) , které vynutily konkrétní ovládací prvky cryptograph a auditují použití slabého kryptografického nastavení. Porozumět tomu, kde vaše prostředky Azure můžou mít neoptimální Kryptografické konfigurace, můžou podniknout nápravné akce, které zajistí konfiguraci prostředků v souladu s vašimi zásadami zabezpečení informací. Konkrétně zásady přiřazené tímto plánem vyžadují transparentní šifrování dat v databázích SQL. Auditovat chybějící šifrování u účtů úložiště a proměnných účtu Automation. K dispozici jsou také zásady, které řeší nezabezpečená připojení k účtům úložiště, aplikacím funkcí, WebApp, API Apps a Redis Cache a auditují nešifrované Service Fabric komunikace.

- Function App by měl být přístupný jenom přes HTTPS
- Webová aplikace by měla být přístupná jen přes protokol HTTPS
- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Je třeba povolit transparentní šifrování dat databází SQL.
- Na virtuálních počítačích by se mělo použít šifrování disku
- Proměnné účtu Automation by se měly šifrovat.
- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.
- Je třeba povolit transparentní šifrování dat databází SQL.
- Nasazení transparentního šifrování dat databáze SQL

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5,1, 6,2, 6,6 a 11.2.1, kontrola ohrožení zabezpečení a aktualizace systému

Tento podrobný plán vám pomůže spravovat chyby zabezpečení informací o systému pomocí přiřazování [Azure Policy](../../../policy/overview.md) definicí, které monitorují chybějící aktualizace systému, chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center. Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase.

- Monitorovat chybějící Endpoint Protection v Azure Security Center
- Nasadit výchozí rozšíření Microsoft IaaSAntimalware pro Windows Server
- Nasazení detekce hrozeb na SQL serverech
- Na počítače by se měly nainstalovat aktualizace systému
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 a 7.1.3 oddělení povinností

Jenom jeden vlastník předplatného Azure nepovoluje redundanci správy. I když má příliš mnoho vlastníků předplatného Azure, může dojít k navýšení potenciálu pro porušení zabezpečení prostřednictvím účtu napadeného vlastníka. Tento podrobný plán vám pomůže zachovat příslušný počet vlastníků předplatného Azure přiřazením definic [Azure Policy](../../../policy/overview.md) , které auditují počet vlastníků předplatných Azure. Správa oprávnění vlastníka předplatného vám může pomáhat s implementací vhodného oddělení povinností.

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci. 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3,2, 7.2.1, 8.3.1. a a 8.3.1. b Správa privilegovaných přístupových práv

Tento podrobný plán vám pomůže omezit a řídit privilegovaná přístupová práva tím, že přiřadí definice [Azure Policy](../../../policy/overview.md) k auditování externích účtů s oprávněním vlastníka, zápisu a čtení a účtů zaměstnanců s oprávněním vlastníka nebo zápisu, u kterých není povolené ověřování službou Multi-Factor Authentication. Řízení přístupu na základě role v Azure (Azure RBAC) pomáhá spravovat, kdo má přístup k prostředkům Azure. Princip implementace vlastních pravidel služby Azure RBAC vám může pomáhat ověřit potřebnou a správnou implementaci, protože vlastní pravidla služby Azure RBAC jsou náchylná k chybám. Tento podrobný plán také přiřadí [Azure Policy](../../../policy/overview.md) definice k auditu používání ověřování Azure Active Directory pro servery SQL. Použití ověřování Azure Active Directory zjednodušuje správu oprávnění a centralizaci správy identit uživatelů databáze a dalších společností Microsoft.  
services.
 
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.
- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.
- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.
- Pro SQL servery by se měl zřídit správce Azure Active Directory.
- Auditovat využití vlastních pravidel RBAC

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>Minimální oprávnění 8.1.2 a 8.1.5 a kontrola přístupových práv uživatelů

Řízení přístupu na základě role Azure (Azure RBAC) pomáhá spravovat, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice pro audit účtů, které by měly být nastavené na kontrolu, včetně odpisů účtů a externích účtů se zvýšenými oprávněními.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 odebrání nebo úpravy přístupových práv

Řízení přístupu na základě role Azure (Azure RBAC) pomáhá spravovat, kdo má přístup k prostředkům v Azure. Pomocí Azure Active Directory a Azure RBAC můžete aktualizovat role uživatelů tak, aby odrážely změny v organizaci. V případě potřeby se můžou účty zablokovat (nebo odebírat), což okamžitě odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice k auditu účtu, který by měl být považován za odebrání.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3. a, b, 8.2.4. a, b a 8.2.5 ověřování založené na heslech

Tento podrobný plán vám pomůže vymáhat silná hesla přiřazením [Azure Policy](../../../policy/overview.md) definic, které auditují virtuální počítače s Windows, které nevyžadují minimální sílu a jiné požadavky na heslo. Dostupnost virtuálních počítačů v rozporu s zásadami síly hesla vám pomůže provést nápravné akce, které zajistí, že hesla pro všechny uživatelské účty virtuálních počítačů jsou kompatibilní se zásadami.

- \[Verze Preview \] : Auditovat virtuální počítače s Windows, které nemají maximální stáří hesla 70 dní
- \[Verze Preview \] : nasaďte požadavky na auditovat virtuální počítače s Windows, které nemají maximální stáří hesla 70 dní.
- \[Verze Preview \] : Auditovat virtuální počítače s Windows, které neomezují minimální délku hesla na 14 znaků
- \[Verze Preview \] : nasaďte požadavky na auditovat virtuální počítače s Windows, které neomezují minimální délku hesla na 14 znaků.
- \[Verze Preview \] : Auditovat virtuální počítače s Windows, které umožňují opakované použití předchozích 24 hesel
- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů s Windows, které umožňují opakované použití předchozích 24 hesel

## <a name="103-and-1054-audit-generation"></a>10,3 a 10.5.4 pro generování auditu

Tento podrobný plán vám pomůže zajistit, aby byly systémové události zaznamenávány přiřazením [Azure Policy](../../../policy/overview.md) definicí, které auditují nastavení protokolů v prostředcích Azure.
Diagnostické protokoly poskytují přehled o operacích, které byly provedeny v rámci prostředků Azure. Protokoly Azure spoléhají na synchronizované interní hodiny, aby se vytvořil časově korelační záznam událostí mezi prostředky.

- Auditování by mělo být povolené pro pokročilá nastavení zabezpečení dat na SQL Server
- Auditování nastavení diagnostiky
- Auditovat nastavení auditování na úrovni SQL serveru
- Nasazení auditování na SQL serverech
- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.
- Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.

## <a name="1236-and-1237-information-security"></a>Zabezpečení informací 12.3.6 a 12.3.7

Tento podrobný plán vám pomůže se správou a řízením sítě pomocí přiřazování [Azure Policy](../../../policy/overview.md) definic, které auditují přijatelná síťová umístění a schválené podnikové produkty povolené pro dané prostředí. Jednotlivé společnosti přizpůsobují přizpůsobení prostřednictvím parametrů zásad v rámci každé z těchto zásad.

- Povolená umístění
- Povolená umístění pro skupiny prostředků

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacího prvku PCI-DSS v 3.2.1, Projděte si následující články, kde se dozvíte o přehledu a jak nasadit tuto ukázku:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 podrobný plán – přehled](./index.md) 
>  [PCI-DSS v 3.2.1 podrobný plán – postup nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)