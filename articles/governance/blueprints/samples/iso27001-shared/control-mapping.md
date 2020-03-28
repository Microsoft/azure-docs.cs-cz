---
title: Ovládací prvky ukázkového plánu sdílených služeb ISO 27001
description: Mapování řízení ukázky podrobného plánu sdílených služeb ISO 27001. Každý ovládací prvek je mapován na jednu nebo více zásad Azure, které pomáhají s hodnocením.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 6c03da7d5d4caada9ef47a828163a79a003bea93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922524"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Mapování ovládacího prvku ukázky podrobného plánu sdílených služeb ISO 27001

V následujícím článku je podrobně popisuje, jak azure plány ISO 27001 sdílené služby podrobný plán ukázka mapuje na ovládací prvky ISO 27001. Další informace o ovládacích prvcích naleznete v [tématu ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Následující mapování jsou na ovládací prvky **ISO 27001:2013.** Pomocí navigace vpravo přejděte přímo na konkrétní mapování ovládacího prvku. Mnoho mapovaných ovládacích prvků se implementuje pomocí iniciativy [Zásad azure.](../../../policy/overview.md) Pokud chcete zkontrolovat celou iniciativu, otevřete **zásady** na webu Azure portal a vyberte stránku **Definice.** Potom vyhledejte a vyberte ** \[ovládací prvky Preview\] Audit ISO 27001:2013 a nasaďte konkrétní rozšíření virtuálních her, abyste podpořili** integrovanou iniciativu zásad na požadavky auditu.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definic [zásad Azure.](../../../policy/overview.md) Tyto zásady vám mohou pomoci [posoudit dodržování](../../../policy/how-to/get-compliance-data.md) ovládacího prvku; však často není 1:1 nebo úplnou shodu mezi ovládacím prvkem a jednu nebo více zásad. Jako **takový, kompatibilní** v zásadách Azure odkazuje pouze na zásady samotné; Tím nezajistíte, že jste plně kompatibilní se všemi požadavky ovládacího prvku. Kromě toho standard dodržování předpisů zahrnuje ovládací prvky, které nejsou v tuto chvíli adresovány žádnými definicemi zásad Azure. Dodržování předpisů v zásadách Azure je tedy pouze částečným zobrazením celkového stavu dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi zásad Azure pro tento ukázkový plán dodržování předpisů se může v průběhu času měnit. Chcete-li zobrazit historii změn, podívejte se na [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-shared/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Oddělení povinností

Mít jenom jednoho vlastníka předplatného Azure neumožňuje redundanci správy. Naopak s příliš mnoho vlastníků předplatného Azure můžete zvýšit potenciál pro porušení prostřednictvím ohroženého účtu vlastníka. Tento podrobný plán vám pomůže udržovat odpovídající počet vlastníků předplatného Azure přiřazením dvou definic [zásad Azure,](../../../policy/overview.md) které auditují počet vlastníků pro předplatná Azure. Správa oprávnění vlastníka předplatného vám může pomoci implementovat vhodné oddělení povinností.

- \[Náhled\]: Auditování minimálního počtu vlastníků předplatného
- \[Náhled\]: Auditovat maximální počet vlastníků předplatného

## <a name="a821-classification-of-information"></a>A.8.2.1 Klasifikace informací

[Služba Azure pro zjišťování zranitelnosti SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) vám může pomoci odhalit citlivá data uložená ve vašich databázích a obsahuje doporučení ke klasifikaci dat. Tento podrobný plán přiřazuje [definici zásad Azure](../../../policy/overview.md) k auditování, že chyby zabezpečení zjištěné během kontroly ohrožení zabezpečení SQL jsou opraveny.

- \[Náhled\]: Sledování výsledků hodnocení ohrožení zabezpečení SQL v Centru zabezpečení Azure

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Přístup k sítím a síťovým službám

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC) ke správě, kdo má přístup k prostředkům Azure. Tento podrobný plán vám pomůže řídit přístup k prostředkům Azure přiřazením sedmi definic [zásad Azure.](../../../policy/overview.md) Tyto zásady audit použití typů prostředků a konfigurace, které mohou umožnit více tolerantní přístup k prostředkům.
Principy prostředků, které jsou v rozporu s těmito zásadami vám může pomoci přijmout nápravná opatření k zajištění přístupu prostředků Azure je omezena na oprávněné uživatele.

- \[Preview\]: Nasazení rozšíření virtuálního počítače pro audit účtů virtuálních počítačů s Linuxem bez hesel
- \[Preview\]: Nasazení rozšíření virtuálního počítače pro auditování virtuálního počítače s Linuxem umožňujícího vzdálená připojení z účtů bez hesel
- \[Preview\]: Auditování účtů virtuálních počítačů s Linuxem bez hesel
- \[Preview\]: Auditování virtuálního počítače s Linuxem umožňujícího vzdálená připojení z účtů bez hesel
- Auditování používání klasických účtů úložiště
- Auditování používání klasických virtuálních počítačů
- Auditování virtuálních počítačů, které nepoužívají spravované disky

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Správa privilegovaných přístupových práv

Tento podrobný plán vám pomůže omezit a řídit privilegovaná přístupová práva přiřazením čtyř definic [zásad Azure](../../../policy/overview.md) k auditování externích účtů s oprávněními vlastníka nebo zápisu a účty s oprávněními vlastníka a/nebo zápisu, které nemají povolené vícefaktorové ověřování. Azure implementuje řízení přístupu na základě rolí (RBAC) ke správě, kdo má přístup k prostředkům Azure. Tento podrobný plán také přiřazuje tři definice zásad Azure auditování použití ověřování Azure Active Directory pro servery SQL a service fabric. Použití ověřování Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databází a dalších služeb Microsoftu. Tento podrobný plán také přiřadí definici zásad Azure k auditování použití vlastních pravidel RBAC. Pochopení, kde jsou implementována vlastní pravidla RBAC, vám může pomoci ověřit potřebu a správnou implementaci, protože vlastní pravidla RBAC jsou náchylná k chybám.

- \[Náhled\]: Auditování účtů s oprávněními vlastníka, kteří nejsou povoleny vícefaktorové povolení v rámci předplatného
- \[Náhled\]: Auditování účtů s oprávněními k zápisu, které nejsou povoleny vícefaktorové povolení v předplatném
- \[Náhled\]: Auditování externích účtů s oprávněními vlastníka k předplatnému
- \[Náhled\]: Auditování externích účtů s oprávněními k zápisu na předplatné
- Auditování zřizování správce služby Azure Active Directory pro SQL server
- Auditování využití služby Azure Active Directory pro ověřování klientů ve službě Service Fabric
- Auditování použití vlastních pravidel RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Správa tajných autentizačních informací uživatelů

Tento podrobný plán přiřadí tři definice [zásad Azure](../../../policy/overview.md) k účtům auditování, které nemají povolené vícefaktorové ověřování. Vícefaktorové ověřování pomáhá zabezpečit účty i v případě, že dojde k ohrožení jedné části ověřovacích informací. Sledováním účtů bez povoleného vícefaktorového ověřování můžete identifikovat účty, u kterých může být větší pravděpodobnost ohrožení zabezpečení. Tento podrobný plán také přiřadí dvě definice zásad Azure, které auditují oprávnění k souboru hesel virtuálních zařízení linuxu, aby je upozornili, pokud jsou nastavena nesprávně. Toto nastavení umožňuje provést nápravná opatření k zajištění ověření nejsou ohroženy.

- \[Náhled\]: Auditování účtů s oprávněními vlastníka, kteří nejsou povoleny vícefaktorové povolení v rámci předplatného
- \[Náhled\]: Auditování účtů s oprávněními ke čtení, které nejsou povoleny vícefaktorové povolení v předplatném
- \[Náhled\]: Auditování účtů s oprávněními k zápisu, které nejsou povoleny vícefaktorové povolení v předplatném
- \[Náhled\]: Nasazení rozšíření virtuálního počítače pro auditoprávnění souborů passwd virtuálních zařízení v Linuxu
- \[Náhled\]: Audit linuxového virtuálního počítače /etc/passwd oprávnění souboru jsou nastaveny na 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Přezkoumání přístupových práv uživatelů

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC), které vám pomohou spravovat, kdo má přístup k prostředkům v Azure. Pomocí portálu Azure můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřazuje čtyři definice [zásad Azure](../../../policy/overview.md) k účtům auditování, které by měly být upřednostněny pro kontrolu, včetně odpisovaných účtů a externích účtů se zvýšenými oprávněními.

- \[Náhled\]: Auditování zastaralých účtů u předplatného
- \[Náhled\]: Auditování zastaralých účtů s oprávněními vlastníka k předplatnému
- \[Náhled\]: Auditování externích účtů s oprávněními vlastníka k předplatnému
- \[Náhled\]: Auditování externích účtů s oprávněními k zápisu na předplatné

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Odebrání nebo úprava přístupových práv

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC), které vám pomohou spravovat, kdo má přístup k prostředkům v Azure. Pomocí [Služby Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) a RBAC můžete aktualizovat role uživatelů tak, aby odrážely změny v organizaci. V případě potřeby může být účty blokovány z přihlášení (nebo odebrat), což okamžitě odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřadí dvě definice [zásad Azure](../../../policy/overview.md) auditovat odpisovaný účet, který by měl být považován za odebrání.

- \[Náhled\]: Auditování zastaralých účtů u předplatného
- \[Náhled\]: Auditování zastaralých účtů s oprávněními vlastníka k předplatnému

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Postupy bezpečného přihlašování

Tento podrobný plán přiřadí tři definice zásad Azure k účtům auditování, které nemají povolené vícefaktorové ověřování. Azure Multi-Factor Authentication poskytuje další zabezpečení tím, že vyžaduje druhou formu ověřování a poskytuje silné ověřování. Sledováním účtů bez povoleného vícefaktorového ověřování můžete identifikovat účty, u kterých může být větší pravděpodobnost ohrožení zabezpečení.

- \[Náhled\]: Auditování účtů s oprávněními vlastníka, kteří nejsou povoleny vícefaktorové povolení v rámci předplatného
- \[Náhled\]: Auditování účtů s oprávněními ke čtení, které nejsou povoleny vícefaktorové povolení v předplatném
- \[Náhled\]: Auditování účtů s oprávněními k zápisu, které nejsou povoleny vícefaktorové povolení v předplatném

## <a name="a943-password-management-system"></a>A.9.4.3 Systém správy hesel

Tento podrobný plán pomáhá vynutit silná hesla přiřazením 10 definic [zásad Azure,](../../../policy/overview.md) které auditují virtuální počítače s Windows, které nevynucují minimální sílu a další požadavky na hesla. Povědomí o virtuálních počítačů v rozporu se zásadami pevnosti hesla vám pomůže provést nápravná opatření k zajištění hesla pro všechny uživatelské účty virtuálních počítačů jsou v souladu se zásadami.

- \[Náhled\]: Nasazení rozšíření virtuálního počítače pro auditování požadavků na složitost hesla vynucuje
- \[Náhled\]: Nasazení rozšíření virtuálního počítače pro auditování maximálního stáří hesla pro windows 70 dní
- \[Náhled:\]Nasazení rozšíření virtuálního počítače pro auditování minimálního stáří hesla virtuálního počítače s Windows 1 den
- \[Náhled\]: Nasazení rozšíření virtuálního počítače pro auditování hesel virtuálních aplikací v systému Windows musí mít alespoň 14 znaků
- \[Náhled\]: Nasazení rozšíření virtuálního počítače pro auditování virtuálního počítače se systémem Windows by nemělo povolit předchozí chození 24
- \[Náhled\]: Auditování požadavků na složitost hesla vynucuje audit.
- \[Náhled\]: Auditování maximálního stáří hesla pro virtuální počítače s Windows 70 dní
- \[Náhled\]: Auditwindows VM minimální stáří hesla 1 den
- \[Náhled\]: Auditování hesel virtuálních aplikací systému Windows musí mít alespoň 14 znaků.
- \[Náhled\]: Auditovat virtuální ho schvalovat virtuální počítače se systémem Windows by neměl povolit předchozích 24 hesel

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Zásady používání kryptografických kontrol

Tento podrobný plán vám pomůže vynutit zásady používání ovládacích prvků kryptografu přiřazením 13 definic [zásad Azure,](../../../policy/overview.md) které vynucují specifické ovládací prvky kryptografů a auditují použití slabého kryptografického nastavení.
Pochopení, kde vaše prostředky Azure může mít neoptimální kryptografické konfigurace vám může pomoci provést nápravná opatření k zajištění prostředků jsou nakonfigurovány v souladu s vaší zásady zabezpečení informací. Konkrétně zásady přiřazené tímto podrobným plánem vyžadují šifrování pro účty úložiště objektů blob a účty úložiště datových jezer; vyžadovat transparentní šifrování dat v databázích SQL; auditovat chybějící šifrování na účtech úložiště, databázích SQL, discích virtuálních počítačů a proměnných účtů automatizace; auditujte nezabezpečené připojení k účtům úložiště, funkčním aplikacím, webovým aplikacím, aplikacím API a mezipaměti Redis; audit slabého šifrování hesel virtuálního počítače; a auditujte nešifrovanou komunikaci Service Fabric.

- \[Náhled\]: Auditování přístupu https pouze pro aplikaci funkce
- \[Náhled\]: Auditování přístupu https pouze pro webovou aplikaci
- \[Náhled\]: Auditování přístupu https jenom pro aplikaci API
- \[Preview\]: Auditování chybějícího šifrování objektů blob pro účty úložiště
- \[Náhled\]: Nasazení rozšíření virtuálního počítače pro auditwindows virtuálního počítače by neměla ukládat hesla pomocí reverzibilního šifrování
- \[Náhled\]: Auditovat virtuální počítače se systémem Windows by nemělu ukládat hesla pomocí reverzibilního šifrování
- \[Náhled:\]Monitorování nešifrovaných disků virtuálních počítačů v Azure Security Center
- Audit umožňující šifrování proměnných účtu automatizace
- Audit, který umožňuje pouze zabezpečené připojení k mezipaměti Redis
- Auditovat zabezpečený přenos do účtů úložiště
- Auditujte nastavení vlastnosti ClusterProtectionLevel na EncryptAndSign in Service Fabric
- Auditování stavu transparentního šifrování dat
- Transparentní šifrování dat v databázích SQL by mělo být povoleno

## <a name="a1241-event-logging"></a>A.12.4.1 Protokolování událostí

Tento podrobný plán vám pomůže zajistit, aby se systémové události zaznamenávalo přiřazením sedmi definic [zásad Azure,](../../../policy/overview.md) které auditují nastavení protokolu prostředků Azure.
Diagnostické protokoly poskytují přehled o operacích, které byly provedeny v rámci prostředků Azure.

- \[Náhled\]: Nasazení agenta závislostí auditu – image virtuálního počítače (OS) není uveden
- \[Náhled\]: Nasazení agenta závislostí auditu ve VMSS – image virtuálního počítače (OS) není v seznamu
- \[Náhled\]: Nasazení agenta analýzy protokolů auditu – image virtuálního počítače (OS) není uveden jako seznam
- \[Náhled\]: Nasazení agenta analýzy protokolů auditu ve službě VMSS – image virtuálního počítače (OS) není uveden
- Auditování nastavení diagnostiky
- Auditování nastavení auditování na úrovni serveru SQL
- Auditování by mělo být povoleno v rozšířeném nastavení zabezpečení dat na serveru SQL Server.

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Protokoly správců a operátorů

Tento podrobný plán vám pomůže zajistit, aby se systémové události zaznamenávalo přiřazením sedmi definic zásad Azure, které auditují nastavení protokolu prostředků Azure. Diagnostické protokoly poskytují přehled o operacích, které byly provedeny v rámci prostředků Azure.

- \[Náhled\]: Nasazení agenta závislostí auditu – image virtuálního počítače (OS) není uveden
- \[Náhled\]: Nasazení agenta závislostí auditu ve VMSS – image virtuálního počítače (OS) není v seznamu
- \[Náhled\]: Nasazení agenta analýzy protokolů auditu – image virtuálního počítače (OS) není uveden jako seznam
- \[Náhled\]: Nasazení agenta analýzy protokolů auditu ve službě VMSS – image virtuálního počítače (OS) není uveden
- Auditování nastavení diagnostiky
- Auditování nastavení auditování na úrovni serveru SQL
- Auditování by mělo být povoleno v rozšířeném nastavení zabezpečení dat na serveru SQL Server.

## <a name="a1244-clock-synchronization"></a>Synchronizace hodin A.12.4.4

Tento podrobný plán vám pomůže zajistit, aby se systémové události zaznamenávalo přiřazením sedmi definic zásad Azure, které auditují nastavení protokolu prostředků Azure. Protokoly Azure spoléhají na synchronizované interní hodiny k vytvoření časově korelovaného záznamu událostí napříč prostředky.

- \[Náhled\]: Nasazení agenta závislostí auditu – image virtuálního počítače (OS) není uveden
- \[Náhled\]: Nasazení agenta závislostí auditu ve VMSS – image virtuálního počítače (OS) není v seznamu
- \[Náhled\]: Nasazení agenta analýzy protokolů auditu – image virtuálního počítače (OS) není uveden jako seznam
- \[Náhled\]: Nasazení agenta analýzy protokolů auditu ve službě VMSS – image virtuálního počítače (OS) není uveden
- Auditování nastavení diagnostiky
- Auditování nastavení auditování na úrovni serveru SQL
- Auditování by mělo být povoleno v rozšířeném nastavení zabezpečení dat na serveru SQL Server.

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Instalace softwaru na provozní chod

Adaptivní řízení aplikací je řešení z Azure Security Center, které vám pomůže řídit, které aplikace můžete spouštět na virtuálních počítačích umístěných v Azure. Tento podrobný plán přiřadí definici zásad Azure, která monitoruje změny sady povolených aplikací. Tato funkce vám pomůže řídit instalaci softwaru a aplikací na virtuálních počítačích Azure.

- \[Náhled\]: Sledování možného seznamu aplikací whitelisting v Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Správa technických zranitelností

Tento podrobný plán vám pomůže spravovat slabá místa informačního systému přiřazením pěti definic [zásad Azure,](../../../policy/overview.md) které monitorují chybějící aktualizace systému, chyby zabezpečení operačního systému, chyby zabezpečení SQL a chyby zabezpečení virtuálních strojů v Centru zabezpečení Azure. Azure Security Center poskytuje funkce pro vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase.

- \[Náhled\]: Sledování chybějící ochrany koncového bodu v Azure Security Center
- \[Náhled\]: Sledování chybějících aktualizací systému v Azure Security Center
- \[Náhled\]: Monitorování slabých míst operačního systému v Azure Security Center
- \[Náhled\]: Sledování výsledků hodnocení ohrožení zabezpečení SQL v Centru zabezpečení Azure
- \[Náhled\]: Monitorování slabých míst virtuálních počítačů v Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Omezení instalace softwaru

Adaptivní řízení aplikací je řešení z Azure Security Center, které vám pomůže řídit, které aplikace můžete spouštět na virtuálních počítačích umístěných v Azure. Tento podrobný plán přiřadí definici zásad Azure, která monitoruje změny sady povolených aplikací. Omezení instalace softwaru vám mohou pomoci snížit pravděpodobnost zavedení chyb zabezpečení softwaru.

- \[Náhled\]: Sledování možného seznamu aplikací whitelisting v Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 Síťové ovládací prvky

Tento podrobný plán vám pomůže spravovat a řídit sítě přiřazením definice [zásad Azure,](../../../policy/overview.md) která monitoruje skupiny zabezpečení sítě s tolerantními pravidly. Pravidla, která jsou příliš tolerantní, mohou umožnit nezamýšlený přístup k síti a měla by být přezkoumána. Tento podrobný plán také přiřazuje tři definice zásad Azure, které monitorují nechráněné koncové body, aplikace a účty úložiště. Koncové body a aplikace, které nejsou chráněny bránou firewall, a účty úložiště s neomezeným přístupem mohou umožnit nezamýšlený přístup k informacím obsaženým v informačním systému.

- \[Náhled\]: Sledování tolerantního přístupu k síti v Azure Security Center
- \[Náhled\]: Sledování nechráněných síťových koncových bodů v Azure Security Center
- \[Náhled\]: Sledování nechráněné webové aplikace v Azure Security Center
- Auditovat neomezený přístup k účtům úložiště v síti

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Zásady a postupy pro přenos informací

Podrobný plán vám pomůže zajistit zabezpečení přenosu informací se službami Azure přiřazením dvou definic [zásad Azure](../../../policy/overview.md) k auditování nezabezpečených připojení k účtům úložiště a mezipaměti Redis.

- Audit, který umožňuje pouze zabezpečené připojení k mezipaměti Redis
- Auditovat zabezpečený přenos do účtů úložiště

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacích prvku podrobného plánu sdílených služeb ISO 27001, navštivte následující články, kde se dozvíte o architektuře a o tom, jak nasadit tuto ukázku:

> [!div class="nextstepaction"]
> [Plán sdílených služeb ISO 27001 – přehled](./index.md)
> [podrobný plán sdílených služeb ISO 27001 – nasazení kroků](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).