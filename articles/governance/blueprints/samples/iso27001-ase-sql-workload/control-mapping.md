---
title: Mapování ovládacích prvků – plán úloh ISO 27001 ASE/SQL – ukázka
description: Mapování ovládacího prvku vzorku podrobného plánu úlohy ISO 27001 App Service prostředí/službou SQL Database do Azure Policy a RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b0a2d81d2490ded460f4bbb98ebce0b943ba91df
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010905"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Mapování ovládacího prvku vzorku ISO 27001 ASE/SQL úlohy podrobného plánu

Následující článek podrobně popisuje, jak ukázka Azure plány ISO 27001 ASE/SQL Workload podrobného plánu se mapuje na ovládací prvky ISO 27001 se zásadami. Další informace o ovládacích prvcích najdete v tématu [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Následující mapování **ISO 27001: 2013** ovládacích prvků. Pomocí navigace na pravé straně můžete přejít přímo na určitý ovládací prvek mapování. Mnohé z namapované ovládací prvky jsou implementovány pomocí [Azure Policy](../../../policy/overview.md) iniciativy. Si Pokud chcete projít kompletní iniciativu, otevřete **zásady** v Azure portal a vyberte **definice** stránky. Poté vyhledejte a vyberte  **[Preview] auditu ISO 27001: 2013 ovládací prvky a nasadit konkrétní rozšíření virtuálního počítače pro podporu požadavků na auditování** iniciativa předdefinovaných zásad.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 zodpovědnosti

Pro správu redundance neumožňuje mít pouze jeden vlastník předplatného Azure. Naopak s příliš mnoho vlastníků. předplatné Azure může zvýšit riziko porušení zabezpečení přes účet ohroženým. Tento podrobný plán slouží ke správě odpovídající počet vlastníků předplatných Azure přiřazením dvě [Azure Policy](../../../policy/overview.md) definice, které auditování počet vlastníků předplatných Azure. Správa oprávnění vlastníka předplatného vám můžou pomoct implementovat odpovídající oddělení povinností.

- [Preview]: Audit minimum number of owners for subscription
- [Preview]: Audit maximum number of owners for a subscription

## <a name="a821-classification-of-information"></a>A.8.2.1 klasifikace informací

Azure [službu posouzení ohrožení zabezpečení SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) vám můžou pomoct odhalit citlivá data uložená ve vašich databázích a zahrnuje doporučení, jak klasifikovat tato data. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definici audit, že proběhne náprava podle zjištěné v rámci SQL sken posouzení ohrožení zabezpečení.

- [Preview]: Monitor SQL vulnerability assessment results in Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 přístup k sítím a síťové služby

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC) pro správu, kdo má přístup k prostředkům Azure. Tento podrobný plán umožňuje řídit přístup k prostředkům Azure pomocí přiřazení sedm [Azure Policy](../../../policy/overview.md) definice. Tyto zásady auditování typy prostředků a konfigurace, které vám umožní mnohem mírnější přístup k prostředkům.
Principy prostředky, které jsou v rozporu se tyto zásady lze provést opravné akce k zajištění přístupu k prostředkům Azure nápovědy je omezené na autorizované uživatele.

- [Preview]: Deploy VM extension to audit Linux VM accounts with no passwords
- [Preview]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  Heslas
- [Preview]: Audit Linux VM accounts with no passwords
- [Preview]: Audit Linux VM allowing remote connections from accounts with no passwords
- Auditovat používání klasických účtů úložiště
- Auditovat používání klasických virtuálních počítačů
- Auditovat virtuální počítače, které nepoužívají spravované disky

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 správy privilegovaného přístupová práva

Tento podrobný plán vám pomůže omezit a řídit práva privilegovaného přístupu přiřazením čtyři [Azure Policy](../../../policy/overview.md) definice a auditovat externí účty s vlastníkem a/nebo zápis oprávnění a účty s vlastníkem a/nebo oprávnění k zápisu které nemají povolené vícefaktorové ověřování. Azure implementuje řízení přístupu na základě rolí (RBAC) pro správu, kdo má přístup k prostředkům Azure. Tento podrobný plán také přiřadí tři definice zásad Azure a kontrolovat použití ověřování Azure Active Directory pro SQL servery a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje správu oprávnění zjednodušené a centralizované identity management uživatelů databáze a dalším službám společnosti Microsoft. Tento podrobný plán také přiřadí definici zásad Azure kontrolovat použití vlastních pravidel RBAC. Principy, kde jsou implementovat vlastní pravidla RBAC můžete ověřit potřebujete a správnou implementaci vlastní pravidla RBAC jsou náchylné k chybám.

- [Preview]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Preview]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Preview]: Audit external accounts with owner permissions on a subscription
- [Preview]: Audit external accounts with write permissions on a subscription
- Auditovat zřizování správce Azure Active Directory pro server SQL
- Auditovat používání Azure Active Directory k ověřování klientů v Service Fabricu
- Auditovat používání vlastních pravidel RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>Správa A.9.2.4 informace secret ověřování uživatelů

Tento podrobný plán přiřadí tři [Azure Policy](../../../policy/overview.md) definice auditovat účty, které nemají povolené vícefaktorové ověřování. Multi-Factor authentication pomáhá zabezpečit účty i v případě, že dojde k narušení jednu část ověřovacích informací. Díky monitorování účtů bez povolení ověřování službou Multi-Factor Authentication, můžete určit účty, které mohou být pravděpodobně došlo k narušení. Tento podrobný plán také přiřadí dvěma definicemi zásad Azure, které audit virtuálních počítačů s Linuxem heslo souboru oprávnění pro výstrahy v případě, že máte správně nastavené. Toto nastavení umožňuje využít nápravné akce, která Ujistěte se, že nebudou ohroženy ověřovací data.

- [Preview]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Preview]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Preview]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Preview]: Deploy VM extension to audit Linux VM passwd file permissions
- [Preview]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>Přehled A.9.2.5 uživatelská přístupová práva

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC), které vám pomůžou spravovat, kdo má přístup k prostředkům v Azure. Pomocí webu Azure portal, můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřadí čtyři [Azure Policy](../../../policy/overview.md) definice auditovat účty, které by měl být nastaveno jako prioritní ke kontrole, včetně odepsanou účtů a externí účty se zvýšenými oprávněními.

- [Preview]: Audit deprecated accounts on a subscription
- [Preview]: Audit deprecated accounts with owner permissions on a subscription
- [Preview]: Audit external accounts with owner permissions on a subscription
- [Preview]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>Odebrání A.9.2.6 nebo úpravu přístupová práva

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC), které vám pomůžou spravovat, kdo má přístup k prostředkům v Azure. Pomocí [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) a RBAC, můžete aktualizovat role uživatele tak, aby odrážely změn v organizaci. Pokud je nepotřebujete, účtů může blokovat přihlášení (nebo odstranit), která okamžitě Odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřadí dva [Azure Policy](../../../policy/overview.md) definice auditovat odepsanou účet, který by měl být pro odebrání.

- [Preview]: Audit deprecated accounts on a subscription
- [Preview]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 zabezpečené přihlašování postupy

Tento podrobný plán přiřadí tři definic Azure Policy auditovat účty, které nemají povolené vícefaktorové ověřování. Azure Multi-Factor Authentication poskytuje dodatečné zabezpečení vyžadováním druhou formu ověřování a poskytuje silné ověřování. Díky monitorování účtů bez povolení ověřování službou Multi-Factor Authentication, můžete určit účty, které mohou být pravděpodobně došlo k narušení.

- [Preview]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Preview]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Preview]: Audit accounts with write permissions who are not MFA enabled on a subscription

## <a name="a943-password-management-system"></a>Systém správy A.9.4.3 heslo

Tento podrobný plán umožňuje vynucování silných hesel přiřazením 10 [Azure Policy](../../../policy/overview.md) definice, které auditovat virtuální počítače s Windows, který nevynucují minimální sílu a další požadavky na heslo. Sledování virtuálních počítačů v rozporu se zásadami sílu hesel umožňuje provést opravné akce k zajištění, že jsou kompatibilní s zásady hesla pro všechny uživatelské účty virtuálního počítače.

- [Preview]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [Preview]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [Preview]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [Preview]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [Preview]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [Preview]: Audit Windows VM enforces password complexity requirements
- [Preview]: Audit Windows VM maximum password age 70 days
- [Preview]: Audit Windows VM minimum password age 1 day
- [Preview]: Audit Windows VM passwords must be at least 14 characters
- [Preview]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 zásady týkající se použití kryptografie

Tento podrobný plán umožňuje vynucovat zásady týkající se použití ovládacích prvků cryptograph přiřazením 13 [Azure Policy](../../../policy/overview.md) definice, které vynucují konkrétní cryptograph ovládací prvky a auditu použití nastavení slabého šifrování.
Principy, kde vašich prostředků Azure může mít optimální kryptografické konfigurace můžete provést opravné akce k zajištění, že prostředky jsou nakonfigurované v souladu se zásadami zabezpečení vašich informací. Konkrétně zásady přiřazené službou Tento podrobný plán vyžadovat šifrování pro účty blob storage a účty úložiště data lake; vyžadovat transparentní šifrování dat v databázích SQL; Auditovat chybějící šifrování na účty úložiště, databáze SQL, disky virtuálních počítačů a proměnných účtu automation; Auditovat nezabezpečené připojení k účtům úložiště, aplikace Function App, webové aplikace, aplikace API a Redis Cache Auditovat šifrování hesla malý virtuální počítač; a auditovat nešifrovaná komunikace Service Fabric.

- [Preview]: Audit HTTPS only access for a Function App
- [Preview]: Audit HTTPS only access for a Web Application
- [Preview]: Audit HTTPS only access for an API App
- [Preview]: Audit missing blob encryption for storage accounts
- [Preview]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  šifrovánín
- [Preview]: Audit Windows VM should not store passwords using reversible encryption
- [Preview]: Monitor unencrypted SQL database in Azure Security Center
- [Preview]: Monitor unencrypted VM Disks in Azure Security Center
- Auditovat povolení šifrování proměnných účtu Automation
- Auditovat povolení výhradně zabezpečených připojení k Redis Cache
- Auditovat zabezpečený přenos na účty úložiště
- Auditovat nastavení vlastnosti ClusterProtectionLevel v Service Fabricu na EncryptAndSign
- Auditování stavu transparentního šifrování dat

## <a name="a1241-event-logging"></a>Protokolování událostí A.12.4.1

Tento podrobný plán vám pomůže zajistit systémové události jsou protokolovány přiřazením sedm [Azure Policy](../../../policy/overview.md) definice, které auditování nastavení protokolu pro prostředky Azure.
Diagnostické protokoly poskytují přehled o operacích provedených v rámci prostředků Azure.

- [Preview]: Nasazení agenta závislostí auditu - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Auditovat nasazení agenta závislostí v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Auditovat nasazení agenta Log Analytics v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Monitor unaudited SQL database in Azure Security Center
- Auditování nastavení diagnostiky
- Auditovat nastavení auditování SQL na úrovni serveru

## <a name="a1243-administrator-and-operator-logs"></a>Protokoly správce A.12.4.3 and – operátor

Tento podrobný plán vám pomůže zajistit, že systémové události jsou zaznamenána přiřazením sedm definic Azure Policy, které auditování nastavení protokolu pro prostředky Azure. Diagnostické protokoly poskytují přehled o operacích provedených v rámci prostředků Azure.

- [Preview]: Nasazení agenta závislostí auditu - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Auditovat nasazení agenta závislostí v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Auditovat nasazení agenta Log Analytics v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Monitor unaudited SQL database in Azure Security Center
- Auditování nastavení diagnostiky
- Auditovat nastavení auditování SQL na úrovni serveru

## <a name="a1244-clock-synchronization"></a>Synchronizace hodin A.12.4.4

Tento podrobný plán vám pomůže zajistit, že systémové události jsou zaznamenána přiřazením sedm definic Azure Policy, které auditování nastavení protokolu pro prostředky Azure. Protokoly Azure využívají k vytvoření záznamu čas korelační události napříč prostředky synchronizovat interní hodiny.

- [Preview]: Nasazení agenta závislostí auditu - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Auditovat nasazení agenta závislostí v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Auditovat nasazení agenta Log Analytics v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- [Preview]: Monitor unaudited SQL database in Azure Security Center
- Auditování nastavení diagnostiky
- Auditovat nastavení auditování SQL na úrovni serveru

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 instalace softwaru na provozní systémy

Adaptivní řízení aplikací je řešení ze služby Azure Security Center, které pomáhá řídit aplikace, které můžete spouštět na vašich virtuálních počítačích v Azure. Tento podrobný plán přiřadí definici rozhraní Azure Policy, která sleduje změny sadu povolených aplikací. Tato funkce umožňuje řídit instalaci softwaru a aplikací na virtuálních počítačích Azure.

- [Preview]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>Správa A.12.6.1 technické ohrožení zabezpečení

Tento podrobný plán slouží ke správě systému informace o ohrožení zabezpečení přiřazením pět [Azure Policy](../../../policy/overview.md) definice, které monitorovat chybějící aktualizace systému, ohrožení zabezpečení operačního systému, SQL ohrožení zabezpečení a virtuální počítač ohrožení zabezpečení v Azure Security Center. Azure Security Center nabízí možnosti vytváření sestav, které vám umožní mít v reálném čase přehled o stavu zabezpečení nasazených prostředků Azure.

- [Preview]: Monitor missing Endpoint Protection in Azure Security Center
- [Preview]: Monitor missing system updates in Azure Security Center
- [Preview]: Monitor OS vulnerabilities in Azure Security Center
- [Preview]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Preview]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 omezení týkající se instalace softwaru

Adaptivní řízení aplikací je řešení ze služby Azure Security Center, které pomáhá řídit aplikace, které můžete spouštět na vašich virtuálních počítačích v Azure. Tento podrobný plán přiřadí definici rozhraní Azure Policy, která sleduje změny sadu povolených aplikací. Omezení týkající se instalace softwaru můžete snížit pravděpodobnost, že zavedení chybami zabezpečení softwaru.

- [Preview]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>Ovládací prvky A.13.1.1 sítě

Tento podrobný plán umožňuje spravovat a řídit sítě tak, že přiřadíte [Azure Policy](../../../policy/overview.md) definice, která monitoruje skupiny zabezpečení sítě s benevolentními pravidly. Pravidla, která jsou příliš benevolentními může povolit nežádoucí síťový přístup a byste měli zkontrolovat. Tento podrobný plán také přiřadí tři definic Azure Policy, monitorovat nechráněné koncové body, aplikacím a účtům úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem můžete povolit nežádoucí přístup informace obsažené v informačním systému.

- [Preview]: Monitor permissive network access in Azure Security Center
- [Preview]: Monitor unprotected network endpoints in Azure Security Center
- [Preview]: Monitor unprotected web application in Azure Security Center
- Auditovat neomezený síťový přístup k účtům úložiště

## <a name="a1321-information-transfer-policies-and-procedures"></a>Zásady přenosu A.13.2.1 informace a postupy

Podrobný plán vám pomůže zajistit přenosu informací pomocí služeb Azure je zabezpečený přiřazením dvě [Azure Policy](../../../policy/overview.md) definice auditovat nezabezpečené připojení k účtům úložiště a Redis Cache.

- Auditovat povolení výhradně zabezpečených připojení k Redis Cache
- Auditovat zabezpečený přenos na účty úložiště

## <a name="next-steps"></a>Další postup

Teď, když jste zkontrolovali mapování ovládacího prvku vzorku podrobného plánu úlohy ISO 27001 App Service prostředí/službou SQL Database, najdete v následujících článcích najdete informace o architektuře a jak nasadit tuto ukázku:

> [!div class="nextstepaction"]
> [Podrobný plán úloh ISO 27001 App Service prostředí/službou SQL Database – přehled](./index.md)
> [ISO 27001 App Service prostředí/službou SQL Database úlohy podrobný plán – kroky nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)