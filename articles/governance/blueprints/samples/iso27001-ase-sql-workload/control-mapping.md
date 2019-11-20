---
title: ISO 27001 pomocného a mapování úloh podrobného řízení pro úlohy SQL
description: Mapování ovládacího prvku podrobného plánu úloh App Service Environment/SQL Database ISO 27001 na Azure Policy a RBAC.
ms.date: 11/18/2019
ms.topic: sample
ms.openlocfilehash: ab650e35537213b0ce1e1d2b2f4692a09b0995ae
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184550"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Mapování ovládacího prvku ukázka ISO 27001 pomocného programu/úlohy SQL

Následující článek podrobně popisuje, jak ukázka podrobného plánu úloh pro Azure modrotisky ISO 27001 a SQL úlohy v plánech. mapuje na ovládací prvky ISO 27001. Další informace o ovládacích prvcích naleznete v tématu [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Následující mapování jsou pro ovládací prvky **ISO 27001:2013** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte **\[Preview\] auditovat kontrolní mechanismy ISO 27001:2013 a nasaďte specifická rozšíření virtuálních počítačů, která budou podporovat požadavky auditu na** integrovanou iniciativu zásad.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-ase-sql-workload/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A. 6.1.2 oddělení povinností

Jenom jeden vlastník předplatného Azure nepovoluje redundanci správy. I když má příliš mnoho vlastníků předplatného Azure, může dojít k navýšení potenciálu pro porušení zabezpečení prostřednictvím účtu napadeného vlastníka. Tento podrobný plán vám pomůže zachovat příslušný počet vlastníků předplatného Azure tím, že přiřadí dvě definice [Azure Policy](../../../policy/overview.md) , které auditují počet vlastníků předplatných Azure. Správa oprávnění vlastníka předplatného vám může pomáhat s implementací vhodného oddělení povinností.

- \[Preview\]: Auditovat minimální počet vlastníků pro předplatné
- \[Preview\]: Auditovat maximální počet vlastníků předplatného

## <a name="a821-classification-of-information"></a>A. 8.2.1 klasifikace informací

[Služba posouzení ohrožení zabezpečení SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) Azure vám může pomáhat najít citlivá data uložená ve vašich databázích a obsahuje doporučení ke klasifikaci těchto dat. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) k vyhodnocení, že chyby zabezpečení zjištěné při kontrole posouzení ohrožení zabezpečení SQL jsou opraveny.

- \[Preview\]: sledujte výsledky posouzení ohrožení zabezpečení SQL v Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A. 9.1.2 přístup k sítím a síťovým službám

Azure implementuje [řízení přístupu na základě role](../../../../role-based-access-control/overview.md) (RBAC), které umožňuje spravovat, kdo má přístup k prostředkům Azure. Tento podrobný plán vám pomůže řídit přístup k prostředkům Azure přiřazením sedmi [Azure Policych](../../../policy/overview.md) definic. Tyto zásady auditují použití typů prostředků a konfigurací, které by mohly umožňovat více opravňující přístup k prostředkům.
Principy prostředků, které jsou v rozporu s těmito zásadami, vám pomůžou podniknout nápravné akce, které zajistí, že přístup k prostředkům Azure bude omezený na autorizované uživatele.

- \[Preview\]: nasazení rozšíření virtuálního počítače pro audit účtů virtuálních počítačů se systémem Linux bez hesel
- \[Preview\]: nasazení rozšíření virtuálního počítače pro audit virtuálních počítačů se systémem Linux umožňující vzdálená připojení z účtů bez hesel
- \[Preview\]: Auditovat účty virtuálních počítačů Linux bez hesla
- \[Preview\]: Auditovat virtuální počítač Linux umožňující vzdálená připojení z účtů bez hesla
- Auditovat používání klasických účtů úložiště
- Auditovat používání klasických virtuálních počítačů
- Auditovat virtuální počítače, které nepoužívají spravované disky

## <a name="a923-management-of-privileged-access-rights"></a>A. 9.2.3 Správa privilegovaných přístupových práv

Tento podrobný plán vám pomůže omezit a řídit privilegovaná přístupová práva tím, že přiřazuje čtyři definice [Azure Policy](../../../policy/overview.md) k auditu externích účtů s oprávněním vlastníka nebo zápisu a s oprávněními k zápisu a s oprávněním k zápisu, u kterých není povolené ověřování službou Multi-Factor Authentication. Azure implementuje řízení přístupu na základě role (RBAC), které umožňuje spravovat, kdo má přístup k prostředkům Azure. Tento podrobný plán také přiřadí tři Azure Policy definice k auditu používání ověřování Azure Active Directory pro servery SQL a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu. Tento podrobný plán také přiřadí definici Azure Policy pro audit používání vlastních pravidel RBAC. Princip implementace vlastních pravidel RBAC vám může pomáhat při ověřování potřeb a správné implementace, protože vlastní pravidla RBAC jsou náchylná k chybám.

- \[Preview\]: audit účtů s oprávněními vlastníka, kteří nejsou u předplatného povolená MFA
- \[Preview\]: audit účtů s oprávněními k zápisu, kteří nejsou u předplatného povolená MFA
- \[Preview\]: Auditovat externí účty s oprávněním vlastníka u předplatného
- \[Preview\]: Auditovat externí účty s oprávněním k zápisu do předplatného
- Audit zřizování správce Azure Active Directory pro SQL Server
- Auditovat využití Azure Active Directory pro ověřování klientů v Service Fabric
- Auditovat využití vlastních pravidel RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A. 9.2.4 Správa tajných ověřovacích informací uživatelů

Tento podrobný plán přiřadí tři [Azure Policy](../../../policy/overview.md) definice pro audit účtů, u kterých není povolené Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější. Tento podrobný plán také přiřadí dvě definice Azure Policy, které auditují oprávnění souboru hesla virtuálního počítače Linux pro upozornění, pokud jsou nesprávně nastaveny. Tato instalace vám umožní provést nápravná opatření, aby nedošlo k ohrožení ověřovatelů.

- \[Preview\]: audit účtů s oprávněními vlastníka, kteří nejsou u předplatného povolená MFA
- \[Preview\]: audit účtů s oprávněními ke čtení, která nejsou u předplatného povolená MFA
- \[Preview\]: audit účtů s oprávněními k zápisu, kteří nejsou u předplatného povolená MFA
- \[Preview\]: nasazení rozšíření virtuálního počítače pro audit oprávnění k souboru passwd virtuálního počítače se systémem Linux
- \[Preview\]: oprávnění k souboru auditu/etc/passwd virtuálního počítače pro Linux jsou nastavena na 0644.

## <a name="a925-review-of-user-access-rights"></a>A. 9.2.5 Kontrola přístupových práv uživatele

Azure implementuje [řízení přístupu na základě role](../../../../role-based-access-control/overview.md) (RBAC), které vám umožní spravovat, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřadí čtyři [Azure Policy](../../../policy/overview.md) definice pro audit účtů, které by měly být nastavené na kontrolu, včetně odpisů účtů a externích účtů se zvýšenými oprávněními.

- \[Preview\]: audit zastaralých účtů v předplatném
- \[Preview\]: audit zastaralých účtů s oprávněním vlastníka u předplatného
- \[Preview\]: Auditovat externí účty s oprávněním vlastníka u předplatného
- \[Preview\]: Auditovat externí účty s oprávněním k zápisu do předplatného

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A. 9.2.6 odebrání nebo úpravy přístupových práv

Azure implementuje [řízení přístupu na základě role](../../../../role-based-access-control/overview.md) (RBAC), které vám umožní spravovat, kdo má přístup k prostředkům v Azure. Pomocí [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) a RBAC můžete aktualizovat role uživatelů tak, aby odrážely změny v organizaci. V případě potřeby se můžou účty zablokovat (nebo odebírat), což okamžitě odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřadí dvě [Azure Policy](../../../policy/overview.md) definice k auditu účtu, který by měl být považován za odebrání.

- \[Preview\]: audit zastaralých účtů v předplatném
- \[Preview\]: audit zastaralých účtů s oprávněním vlastníka u předplatného

## <a name="a942-secure-log-on-procedures"></a>Postupy zabezpečeného přihlašování. 9.4.2

Tento podrobný plán přiřadí tři Azure Policy definice pro audit účtů, u kterých není povolené Multi-Factor Authentication. Azure Multi-Factor Authentication poskytuje dodatečné zabezpečení tím, že vyžaduje druhou formu ověřování a zajišťuje silné ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- \[Preview\]: audit účtů s oprávněními vlastníka, kteří nejsou u předplatného povolená MFA
- \[Preview\]: audit účtů s oprávněními ke čtení, která nejsou u předplatného povolená MFA
- \[Preview\]: audit účtů s oprávněními k zápisu, kteří nejsou u předplatného povolená MFA

## <a name="a943-password-management-system"></a>A. 9.4.3 systém pro správu hesel

Tento podrobný plán vám pomůže vynutilit silná hesla přiřazením 10 [Azure Policych](../../../policy/overview.md) definic, které auditují virtuální počítače s Windows, které nevyžadují minimální sílu a jiné požadavky na heslo. Dostupnost virtuálních počítačů v rozporu s zásadami síly hesla vám pomůže provést nápravné akce, které zajistí, že hesla pro všechny uživatelské účty virtuálních počítačů jsou kompatibilní se zásadami.

- \[Preview\]: nasazení rozšíření virtuálního počítače pro audit virtuálních počítačů s Windows vynutila požadavky na složitost hesla
- \[Preview\]: nasazení rozšíření virtuálního počítače pro audit maximálního stáří hesla Windows VM 70 dní
- \[Preview\]: nasazení rozšíření virtuálního počítače pro audit minimálního stáří hesla virtuálního počítače s Windows 1 den
- \[Preview\]: nasazení rozšíření virtuálního počítače pro audit hesel virtuálních počítačů s Windows musí mít aspoň 14 znaků.
- \[Preview\]: nasazení rozšíření virtuálního počítače pro audit virtuálních počítačů s Windows nesmí umožňovat předchozí 24 hesla
- \[Preview\]: Audituje virtuální počítač s Windows, který vynutil požadavky na složitost hesla
- \[Preview\]: Auditovat maximální stáří hesla pro Windows VM 70 dní
- \[Preview\]: Auditovat minimální stáří hesla virtuálního počítače s Windows 1 den
- \[Preview\]: audit hesel virtuálních počítačů s Windows musí mít aspoň 14 znaků.
- \[Preview\]: Auditovat virtuální počítač s Windows nesmí umožňovat předchozí 24 hesla

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Zásady. 10.1.1 pro použití kryptografických ovládacích prvků

Tento podrobný plán vám pomůže vyhovět zásadám používání ovládacích prvků cryptograph přiřazením 13 [Azure Policych](../../../policy/overview.md) definic, které vysazují konkrétní ovládací prvky cryptograph a auditují použití slabého kryptografického nastavení.
Porozumět tomu, kde vaše prostředky Azure můžou mít neoptimální Kryptografické konfigurace, můžou podniknout nápravné akce, které zajistí konfiguraci prostředků v souladu s vašimi zásadami zabezpečení informací. Konkrétně zásady přiřazené tímto plánem vyžadují šifrování pro účty úložiště BLOB a účty Data Lake Storage. vyžadovat transparentní šifrování dat na databázích SQL; Auditovat chybějící šifrování u účtů úložiště, databází SQL, disků virtuálních počítačů a proměnných účtu Automation; auditujte nezabezpečená připojení k účtům úložiště, aplikacím funkcí, webové aplikaci, API Apps a Redis Cache; Auditovat slabé šifrování hesla virtuálního počítače; a auditujte nešifrované Service Fabric komunikaci.

- \[Preview\]: Auditovat přístup jenom přes protokol HTTPS pro Function App
- \[Preview\]: Auditovat přístup jenom přes protokol HTTPS pro webovou aplikaci
- \[Preview\]: Auditovat přístup jenom přes protokol HTTPS pro aplikaci API
- \[Preview\]: Auditovat chybějící šifrování objektů BLOB pro účty úložiště
- \[Preview\]: nasazení rozšíření virtuálního počítače pro audit virtuálních počítačů s Windows nesmí ukládat hesla pomocí reverzibilního šifrování
- \[Preview\]: Auditovat virtuální počítač s Windows by neměl ukládat hesla pomocí reverzibilního šifrování
- \[Preview\]: Sledujte nešifrované disky virtuálních počítačů v Azure Security Center
- Auditovat povolení šifrování proměnných účtu služby Automation
- Auditovat povolení jenom zabezpečených připojení k vašemu Redis Cache
- Auditovat zabezpečený přenos do účtů úložiště
- Auditovat nastavení vlastnosti ClusterProtectionLevel na EncryptAndSign v Service Fabric
- Auditování stavu transparentního šifrování dat
- Je třeba povolit transparentní šifrování dat databází SQL.

## <a name="a1241-event-logging"></a>Protokolování událostí. 12.4.1

Tento podrobný plán vám pomůže zajistit, aby se události systému hlásily pomocí sedmi definic [Azure Policy](../../../policy/overview.md) , které auditují nastavení protokolu v prostředcích Azure.
Diagnostické protokoly poskytují přehled o operacích, které byly provedeny v rámci prostředků Azure.

- \[Preview\]: nasazení Dependency Agent auditu – image virtuálního počítače (OS) není v seznamu
- \[Preview\]: nasazení Dependency Agent pro audit v VMSS-VM Image (OS) není v seznamu.
- \[Preview\]: nasazení agenta Log Analytics auditu – image virtuálního počítače (OS) není v seznamu.
- \[Preview\]: nasazení agenta Log Analytics auditu v VMSS-VM Image (OS) není v seznamu.
- Auditování nastavení diagnostiky
- Auditovat nastavení auditování na úrovni SQL serveru
- Auditování by mělo být povolené pro pokročilá nastavení zabezpečení dat na SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>A. 12.4.3 – protokoly správců a operátorů

Tento podrobný plán vám pomůže zajistit, aby se události systému hlásily pomocí sedmi definic Azure Policy, které auditují nastavení protokolu v prostředcích Azure. Diagnostické protokoly poskytují přehled o operacích, které byly provedeny v rámci prostředků Azure.

- \[Preview\]: nasazení Dependency Agent auditu – image virtuálního počítače (OS) není v seznamu
- \[Preview\]: nasazení Dependency Agent pro audit v VMSS-VM Image (OS) není v seznamu.
- \[Preview\]: nasazení agenta Log Analytics auditu – image virtuálního počítače (OS) není v seznamu.
- \[Preview\]: nasazení agenta Log Analytics auditu v VMSS-VM Image (OS) není v seznamu.
- Auditování nastavení diagnostiky
- Auditovat nastavení auditování na úrovni SQL serveru
- Auditování by mělo být povolené pro pokročilá nastavení zabezpečení dat na SQL Server

## <a name="a1244-clock-synchronization"></a>Synchronizace hodin A. 12.4.4

Tento podrobný plán vám pomůže zajistit, aby se události systému hlásily pomocí sedmi definic Azure Policy, které auditují nastavení protokolu v prostředcích Azure. Protokoly Azure spoléhají na synchronizované interní hodiny, aby se vytvořil časově korelační záznam událostí mezi prostředky.

- \[Preview\]: nasazení Dependency Agent auditu – image virtuálního počítače (OS) není v seznamu
- \[Preview\]: nasazení Dependency Agent pro audit v VMSS-VM Image (OS) není v seznamu.
- \[Preview\]: nasazení agenta Log Analytics auditu – image virtuálního počítače (OS) není v seznamu.
- \[Preview\]: nasazení agenta Log Analytics auditu v VMSS-VM Image (OS) není v seznamu.
- Auditování nastavení diagnostiky
- Auditovat nastavení auditování na úrovni SQL serveru
- Auditování by mělo být povolené pro pokročilá nastavení zabezpečení dat na SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A. 12.5.1 instalace softwaru v operačních systémech

Adaptivní řízení aplikací je řešení z Azure Security Center, které vám pomůže určit, které aplikace se můžou spouštět na virtuálních počítačích umístěných v Azure. Tento podrobný plán přiřadí definici Azure Policy, která monitoruje změny v sadě povolených aplikací. Tato schopnost vám pomůže řídit instalaci softwaru a aplikací na virtuální počítače Azure.

- \[verze Preview\]: Sledujte možné zobrazení seznamu povolených aplikací v Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A. 12.6.1 správu technických ohrožení zabezpečení

Tento podrobný plán vám pomůže spravovat chyby zabezpečení systému pomocí přiřazení pěti [Azure Policych](../../../policy/overview.md) definic, které monitorují chybějící aktualizace systému, chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center. Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase.

- \]\[Preview: monitorovat chybějící Endpoint Protection v Azure Security Center
- \[verze Preview\]: monitorovat chybějící aktualizace systému v Azure Security Center
- \[Preview\]: Sledujte slabá místa zabezpečení operačního systému v Azure Security Center
- \[Preview\]: sledujte výsledky posouzení ohrožení zabezpečení SQL v Azure Security Center
- \[Preview\]: Sledujte ohrožení zabezpečení virtuálních počítačů v Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A. 12.6.2 omezení instalace softwaru

Adaptivní řízení aplikací je řešení z Azure Security Center, které vám pomůže určit, které aplikace se můžou spouštět na virtuálních počítačích umístěných v Azure. Tento podrobný plán přiřadí definici Azure Policy, která monitoruje změny v sadě povolených aplikací. Omezení při instalaci softwaru vám může přispět k omezení pravděpodobnosti zavedení softwarových slabých míst.

- \[verze Preview\]: Sledujte možné zobrazení seznamu povolených aplikací v Azure Security Center

## <a name="a1311-network-controls"></a>Ovládací prvky sítě. 13.1.1

Tento podrobný plán vám pomůže se správou a řízením sítí pomocí přiřazení definice [Azure Policy](../../../policy/overview.md) , která monitoruje skupiny zabezpečení sítě s povolující pravidla. Pravidla, která jsou příliš neoprávněná, mohou umožňovat neúmyslný přístup k síti a měly by být přezkoumány. Tento podrobný plán také přiřadí tři Azure Policy definice, které sledují nechráněné koncové body, aplikace a účty úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem, můžou dovolit neúmyslný přístup k informacím obsaženým v informačním systému.

- \[verze Preview\]: monitorování opravňujícího přístupu k síti v Azure Security Center
- \[Preview\]: monitorovat nechráněné koncové body sítě v Azure Security Center
- \[Preview\]: Sledujte nechráněnou webovou aplikaci v Azure Security Center
- Auditování neomezeného síťového přístupu k účtům úložiště

## <a name="a1321-information-transfer-policies-and-procedures"></a>Zásady a postupy přenosu informací a. 13.2.1

Podrobný plán vám pomůže zajistit, aby přenos informací se službami Azure byl zabezpečený, a to přiřazením dvou [Azure Policych](../../../policy/overview.md) definic pro audit nezabezpečených připojení k účtům úložiště a Redis Cache.

- Auditovat povolení jenom zabezpečených připojení k vašemu Redis Cache
- Auditovat zabezpečený přenos do účtů úložiště

## <a name="next-steps"></a>Další kroky

Teď, když jste si přečetli mapování ovládacích prvků App Service Environment/SQL Database na základě struktury úloh ISO 27001, najdete v následujících článcích informace o architektuře a způsobu nasazení této ukázky:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database úlohy podrobného plánu – přehled](./index.md)
> [ISO 27001 App Service Environment/SQL Database plán úlohy – postup nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)