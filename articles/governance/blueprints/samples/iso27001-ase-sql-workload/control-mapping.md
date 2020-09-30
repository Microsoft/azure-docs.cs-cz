---
title: Ovládací prvky podrobného řízení pro úlohy (ISO 27001)/řízení úloh SQL
description: Mapování ovládacího prvku podrobného plánu úloh App Service Environment/SQL Database ISO 27001 na Azure Policy a Azure RBAC.
ms.date: 07/13/2020
ms.topic: sample
ms.openlocfilehash: 1d537a3447f794c501ac3a33caf6eb01b17bc470
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541216"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Mapování ovládacího prvku ukázka ISO 27001 pomocného programu/úlohy SQL

Následující článek podrobně popisuje, jak ukázka podrobného plánu úloh pro Azure modrotisky ISO 27001 a SQL úlohy v plánech. mapuje na ovládací prvky ISO 27001. Další informace o ovládacích prvcích naleznete v tématu [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Následující mapování jsou pro ovládací prvky **ISO 27001:2013** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte kontrolní seznam ** \[ \] Audit ISO 27001:2013 a nasaďte konkrétní rozšíření virtuálního počítače pro podporu požadavků na** integrovanou iniciativu zásad pro audit.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-ase-sql-workload/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A. 6.1.2 oddělení povinností

Jenom jeden vlastník předplatného Azure nepovoluje redundanci správy. I když má příliš mnoho vlastníků předplatného Azure, může dojít k navýšení potenciálu pro porušení zabezpečení prostřednictvím účtu napadeného vlastníka. Tento podrobný plán vám pomůže zachovat příslušný počet vlastníků předplatného Azure tím, že přiřadí dvě definice [Azure Policy](../../../policy/overview.md) , které auditují počet vlastníků předplatných Azure. Správa oprávnění vlastníka předplatného vám může pomáhat s implementací vhodného oddělení povinností.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.

## <a name="a821-classification-of-information"></a>A. 8.2.1 klasifikace informací

[Služba posouzení ohrožení zabezpečení SQL](../../../../azure-sql/database/sql-vulnerability-assessment.md) Azure vám může pomáhat najít citlivá data uložená ve vašich databázích a obsahuje doporučení ke klasifikaci těchto dat. Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) k vyhodnocení, že chyby zabezpečení zjištěné při kontrole posouzení ohrožení zabezpečení SQL jsou opraveny.

- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno

## <a name="a912-access-to-networks-and-network-services"></a>A. 9.1.2 přístup k sítím a síťovým službám

Azure implementuje [řízení přístupu na základě role Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) , které umožňuje spravovat, kdo má přístup k prostředkům Azure. Tento podrobný plán vám pomůže řídit přístup k prostředkům Azure přiřazením sedmi [Azure Policych](../../../policy/overview.md) definic. Tyto zásady auditují použití typů prostředků a konfigurací, které by mohly umožňovat více opravňující přístup k prostředkům.
Principy prostředků, které jsou v rozporu s těmito zásadami, vám pomůžou podniknout nápravné akce, které zajistí, že přístup k prostředkům Azure bude omezený na autorizované uživatele.

- Nasazení požadavků pro audit virtuálních počítačů se systémem Linux, které mají účty bez hesla
- Nasazení požadavků pro audit virtuálních počítačů Linux, které umožňují vzdálená připojení z účtů bez hesel
- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, které mají účty bez hesla
- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, které umožňují vzdálená připojení z účtů bez hesel
- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.
- Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.
- Auditovat virtuální počítače, které nepoužívají spravované disky

## <a name="a923-management-of-privileged-access-rights"></a>A. 9.2.3 Správa privilegovaných přístupových práv

Tento podrobný plán vám pomůže omezit a řídit privilegovaná přístupová práva tím, že přiřazuje čtyři definice [Azure Policy](../../../policy/overview.md) k auditu externích účtů s oprávněním vlastníka nebo zápisu a s oprávněními k zápisu a s oprávněním k zápisu, u kterých není povolené ověřování službou Multi-Factor Authentication. Řízení přístupu na základě role v Azure (Azure RBAC) pomáhá spravovat, kdo má přístup k prostředkům Azure. Tento podrobný plán také přiřadí tři Azure Policy definice k auditu používání ověřování Azure Active Directory pro servery SQL a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje zjednodušenou správu oprávnění a centralizovanou správu identit uživatelů databáze a dalších služeb Microsoftu. Tento podrobný plán také přiřadí definici Azure Policy pro audit používání vlastních pravidel služby Azure RBAC. Princip implementace vlastních pravidel služby Azure RBAC vám může pomáhat ověřit potřebnou a správnou implementaci, protože vlastní pravidla služby Azure RBAC jsou náchylná k chybám.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.
- Pro SQL servery by se měl zřídit správce Azure Active Directory.
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.
- Auditovat využití vlastních pravidel RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A. 9.2.4 Správa tajných ověřovacích informací uživatelů

Tento podrobný plán přiřadí tři [Azure Policy](../../../policy/overview.md) definice pro audit účtů, u kterých není povolené Multi-Factor Authentication. Multi-Factor Authentication pomáhá udržet zabezpečení účtů i v případě, že dojde k ohrožení bezpečnosti některých informací o ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější. Tento podrobný plán také přiřadí dvě definice Azure Policy, které auditují oprávnění souboru hesla virtuálního počítače Linux pro upozornění, pokud jsou nesprávně nastaveny. Tato instalace vám umožní provést nápravná opatření, aby nedošlo k ohrožení ověřovatelů.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.
- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, u kterých není oprávnění k souboru passwd nastaveno na 0644
- Nasaďte požadavky pro audit virtuálních počítačů Linux, které nemají oprávnění k souboru passwd nastavené na 0644.

## <a name="a925-review-of-user-access-rights"></a>A. 9.2.5 Kontrola přístupových práv uživatele

Azure implementuje [řízení přístupu na základě role Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) , které vám pomůžou se správou toho, kdo má přístup k prostředkům v Azure. Pomocí Azure Portal můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřadí čtyři [Azure Policy](../../../policy/overview.md) definice pro audit účtů, které by měly být nastavené na kontrolu, včetně odpisů účtů a externích účtů se zvýšenými oprávněními.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A. 9.2.6 odebrání nebo úpravy přístupových práv

Azure implementuje [řízení přístupu na základě role Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) , které vám pomůžou se správou toho, kdo má přístup k prostředkům v Azure. Pomocí [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) a Azure RBAC můžete aktualizovat role uživatelů tak, aby odrážely změny v organizaci. V případě potřeby se můžou účty zablokovat (nebo odebírat), což okamžitě odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřadí dvě [Azure Policy](../../../policy/overview.md) definice k auditu účtu, který by měl být považován za odebrání.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.

## <a name="a942-secure-log-on-procedures"></a>Postupy zabezpečeného přihlašování. 9.4.2

Tento podrobný plán přiřadí tři Azure Policy definice pro audit účtů, u kterých není povolené Multi-Factor Authentication. Azure Multi-Factor Authentication poskytuje dodatečné zabezpečení tím, že vyžaduje druhou formu ověřování a zajišťuje silné ověřování. Monitorováním účtů bez povoleného ověřování službou Multi-Factor Authentication můžete identifikovat účty, jejichž zabezpečení může být pravděpodobnější.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.

## <a name="a943-password-management-system"></a>A. 9.4.3 systém pro správu hesel

Tento podrobný plán vám pomůže vynutilit silná hesla přiřazením 10 [Azure Policych](../../../policy/overview.md) definic, které auditují virtuální počítače s Windows, které nevyžadují minimální sílu a jiné požadavky na heslo. Dostupnost virtuálních počítačů v rozporu s zásadami síly hesla vám pomůže provést nápravné akce, které zajistí, že hesla pro všechny uživatelské účty virtuálních počítačů jsou kompatibilní se zásadami.

- Zobrazit výsledky auditu z virtuálních počítačů s Windows, u kterých není povolené nastavení složitosti hesla
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které nemají maximální stáří hesla 70 dní
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které nemají minimální stáří hesla 1 den
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které neomezují minimální délku hesla na 14 znaků
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které umožňují opakované použití předchozích 24 hesel
- Nasazení požadavků pro audit virtuálních počítačů s Windows, u kterých není povolené nastavení složitosti hesla
- Nasaďte požadavky pro audit virtuálních počítačů s Windows, které nemají maximální stáří hesla 70 dnů.
- Nasazení požadavků pro audit virtuálních počítačů s Windows, které nemají minimální stáří hesla 1 den
- Nasaďte požadavky pro audit virtuálních počítačů s Windows, které neomezují minimální délku hesla na 14 znaků.
- Nasazení požadavků pro audit virtuálních počítačů s Windows, které umožňují opakované použití předchozích 24 hesel

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Zásady. 10.1.1 pro použití kryptografických ovládacích prvků

Tento podrobný plán vám pomůže vyhovět zásadám používání ovládacích prvků cryptograph přiřazením 13 [Azure Policych](../../../policy/overview.md) definic, které vysazují konkrétní ovládací prvky cryptograph a auditují použití slabého kryptografického nastavení.
Porozumět tomu, kde vaše prostředky Azure můžou mít neoptimální Kryptografické konfigurace, můžou podniknout nápravné akce, které zajistí konfiguraci prostředků v souladu s vašimi zásadami zabezpečení informací. Konkrétně zásady přiřazené tímto plánem vyžadují šifrování pro účty úložiště BLOB a účty Data Lake Storage. vyžadovat transparentní šifrování dat na databázích SQL; Auditovat chybějící šifrování u účtů úložiště, databází SQL, disků virtuálních počítačů a proměnných účtu Automation; auditujte nezabezpečená připojení k účtům úložiště, aplikacím funkcí, webové aplikaci, API Apps a Redis Cache; Auditovat slabé šifrování hesla virtuálního počítače; a auditujte nešifrované Service Fabric komunikaci.

- Function App by měl být přístupný jenom přes HTTPS
- Webová aplikace by měla být přístupná jen přes protokol HTTPS
- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Nasazení požadavků pro audit virtuálních počítačů s Windows, které neukládají hesla pomocí reverzibilního šifrování
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, které neukládají hesla pomocí reverzibilního šifrování
- Na virtuálních počítačích by se mělo použít šifrování disku
- Proměnné účtu Automation by se měly šifrovat.
- Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.
- Je třeba povolit transparentní šifrování dat databází SQL.

## <a name="a1241-event-logging"></a>Protokolování událostí. 12.4.1

Tento podrobný plán vám pomůže zajistit, aby se události systému hlásily pomocí sedmi definic [Azure Policy](../../../policy/overview.md) , které auditují nastavení protokolu v prostředcích Azure.
Diagnostické protokoly poskytují přehled o operacích, které byly provedeny v rámci prostředků Azure.

- Nasazení agenta závislostí auditu – image virtuálního počítače (OS) není v seznamu
- Auditování nasazení agenta závislostí ve virtuálních počítačích Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- [Preview]: audit Log Analytics Deployment Agent – image virtuálního počítače (OS) není v seznamu.
- Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- Auditování nastavení diagnostiky
- Auditování na SQL serveru by mělo být povolené.

## <a name="a1243-administrator-and-operator-logs"></a>A. 12.4.3 – protokoly správců a operátorů

Tento podrobný plán vám pomůže zajistit, aby se události systému hlásily pomocí sedmi definic Azure Policy, které auditují nastavení protokolu v prostředcích Azure. Diagnostické protokoly poskytují přehled o operacích, které byly provedeny v rámci prostředků Azure.

- Nasazení agenta závislostí auditu – image virtuálního počítače (OS) není v seznamu
- Auditování nasazení agenta závislostí ve virtuálních počítačích Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- [Preview]: audit Log Analytics Deployment Agent – image virtuálního počítače (OS) není v seznamu.
- Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- Auditování nastavení diagnostiky
- Auditování na SQL serveru by mělo být povolené.

## <a name="a1244-clock-synchronization"></a>Synchronizace hodin A. 12.4.4

Tento podrobný plán vám pomůže zajistit, aby se události systému hlásily pomocí sedmi definic Azure Policy, které auditují nastavení protokolu v prostředcích Azure. Protokoly Azure spoléhají na synchronizované interní hodiny, aby se vytvořil časově korelační záznam událostí mezi prostředky.

- Nasazení agenta závislostí auditu – image virtuálního počítače (OS) není v seznamu
- Auditování nasazení agenta závislostí ve virtuálních počítačích Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- [Preview]: audit Log Analytics Deployment Agent – image virtuálního počítače (OS) není v seznamu.
- Audit Log Analyticsho nasazení agenta ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu
- Auditování nastavení diagnostiky
- Auditování na SQL serveru by mělo být povolené.

## <a name="a1251-installation-of-software-on-operational-systems"></a>A. 12.5.1 instalace softwaru v operačních systémech

Adaptivní řízení aplikací je řešení z Azure Security Center, které vám pomůže určit, které aplikace se můžou spouštět na virtuálních počítačích umístěných v Azure. Tento podrobný plán přiřadí definici Azure Policy, která monitoruje změny v sadě povolených aplikací. Tato schopnost vám pomůže řídit instalaci softwaru a aplikací na virtuální počítače Azure.

- V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.

## <a name="a1261-management-of-technical-vulnerabilities"></a>A. 12.6.1 správu technických ohrožení zabezpečení

Tento podrobný plán vám pomůže spravovat chyby zabezpečení systému pomocí přiřazení pěti [Azure Policych](../../../policy/overview.md) definic, které monitorují chybějící aktualizace systému, chyby zabezpečení operačního systému, chyby zabezpečení SQL a ohrožení zabezpečení virtuálních počítačů v Azure Security Center. Azure Security Center poskytuje možnosti vytváření sestav, které vám umožní získat přehled o stavu zabezpečení nasazených prostředků Azure v reálném čase.

- Monitorovat chybějící Endpoint Protection v Azure Security Center
- Na počítače by se měly nainstalovat aktualizace systému
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="a1262-restrictions-on-software-installation"></a>A. 12.6.2 omezení instalace softwaru

Adaptivní řízení aplikací je řešení z Azure Security Center, které vám pomůže určit, které aplikace se můžou spouštět na virtuálních počítačích umístěných v Azure. Tento podrobný plán přiřadí definici Azure Policy, která monitoruje změny v sadě povolených aplikací. Omezení při instalaci softwaru vám může přispět k omezení pravděpodobnosti zavedení softwarových slabých míst.

- V počítačích by měly být povolené Adaptivní řízení aplikací pro definování bezpečných aplikací.

## <a name="a1311-network-controls"></a>Ovládací prvky sítě. 13.1.1

Tento podrobný plán vám pomůže se správou a řízením sítí pomocí přiřazení definice [Azure Policy](../../../policy/overview.md) , která monitoruje skupiny zabezpečení sítě s povolující pravidla. Pravidla, která jsou příliš neoprávněná, mohou umožňovat neúmyslný přístup k síti a měly by být přezkoumány. Tento podrobný plán také přiřadí tři Azure Policy definice, které sledují nechráněné koncové body, aplikace a účty úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem, můžou dovolit neúmyslný přístup k informacím obsaženým v informačním systému.

- Přístup přes internetový koncový bod by měl být omezený.
- Účty úložiště by měly omezovat síťový přístup

## <a name="a1321-information-transfer-policies-and-procedures"></a>Zásady a postupy přenosu informací a. 13.2.1

Podrobný plán vám pomůže zajistit, aby přenos informací se službami Azure byl zabezpečený, a to přiřazením dvou [Azure Policych](../../../policy/overview.md) definic pro audit nezabezpečených připojení k účtům úložiště a Redis Cache.

- Měla by být povolená jenom zabezpečená připojení k vaší mezipaměti Azure pro Redis.
- Měl by se povolit zabezpečený přenos do účtů úložiště

## <a name="next-steps"></a>Další kroky

Teď, když jste si přečetli mapování ovládacích prvků App Service Environment/SQL Database na základě struktury úloh ISO 27001, najdete v následujících článcích informace o architektuře a způsobu nasazení této ukázky:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database úlohy podrobný plán – přehled](./index.md) 
>  [ISO 27001 App Service Environment/SQL Database úlohy podrobný plán – postup nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
