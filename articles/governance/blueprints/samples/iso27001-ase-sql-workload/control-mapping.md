---
title: Mapování ovládacích prvků – plán úloh ISO 27001 ASE/SQL – ukázka
description: Mapování ovládacího prvku vzorku podrobného plánu úlohy ISO 27001 App Service prostředí/službou SQL Database do Azure Policy a RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 59e47c448f58235114c8fb3147637b77dd5fcf23
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226064"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Mapování ovládacího prvku vzorku ISO 27001 ASE/SQL úlohy podrobného plánu

Následující článek podrobně popisuje, jak ukázka Azure plány ISO 27001 ASE/SQL Workload podrobného plánu se mapuje na ovládací prvky ISO 27001 se zásadami. Další informace o ovládacích prvcích najdete v tématu [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Následující mapování **ISO 27001: 2013** ovládacích prvků. Pomocí navigace na pravé straně můžete přejít přímo na určitý ovládací prvek mapování. Mnohé z namapované ovládací prvky jsou implementovány pomocí [Azure Policy](../../../policy/overview.md) iniciativy. Si Pokud chcete projít kompletní iniciativu, otevřete **zásady** v Azure portal a vyberte **definice** stránky. Poté vyhledejte a vyberte  **\[ve verzi Preview\] auditu ISO 27001: 2013 ovládací prvky a nasadit konkrétní rozšíření virtuálního počítače pro podporu požadavků na auditování** iniciativa předdefinovaných zásad.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 zodpovědnosti

Pro správu redundance neumožňuje mít pouze jeden vlastník předplatného Azure. Naopak s příliš mnoho vlastníků. předplatné Azure může zvýšit riziko porušení zabezpečení přes účet ohroženým. Tento podrobný plán slouží ke správě odpovídající počet vlastníků předplatných Azure přiřazením dvě [Azure Policy](../../../policy/overview.md) definice, které auditování počet vlastníků předplatných Azure. Správa oprávnění vlastníka předplatného vám můžou pomoct implementovat odpovídající oddělení povinností.

- \[Ve verzi Preview\]: Auditovat minimální počet vlastníků pro předplatné
- \[Ve verzi Preview\]: Auditovat maximální počet vlastníků pro odběr

## <a name="a821-classification-of-information"></a>A.8.2.1 klasifikace informací

Azure [službu posouzení ohrožení zabezpečení SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) vám můžou pomoct odhalit citlivá data uložená ve vašich databázích a zahrnuje doporučení, jak klasifikovat tato data. Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definici audit, že proběhne náprava podle zjištěné v rámci SQL sken posouzení ohrožení zabezpečení.

- \[Ve verzi Preview\]: Sledovat výsledky posouzení ohrožení zabezpečení SQL ve službě Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 přístup k sítím a síťové služby

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC) pro správu, kdo má přístup k prostředkům Azure. Tento podrobný plán umožňuje řídit přístup k prostředkům Azure pomocí přiřazení sedm [Azure Policy](../../../policy/overview.md) definice. Tyto zásady auditování typy prostředků a konfigurace, které vám umožní mnohem mírnější přístup k prostředkům.
Principy prostředky, které jsou v rozporu se tyto zásady lze provést opravné akce k zajištění přístupu k prostředkům Azure nápovědy je omezené na autorizované uživatele.

- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření auditovat účty virtuálního počítače s Linuxem pomocí hesla
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření auditovat umožňuje vzdálená připojení z účtů s žádná hesla virtuálního počítače s Linuxem
- \[Ve verzi Preview\]: Auditovat účty virtuálního počítače s Linuxem pomocí hesla
- \[Ve verzi Preview\]: Auditovat umožňuje vzdálená připojení z účtů s žádná hesla virtuálního počítače s Linuxem
- Auditovat použití účty klasického úložiště.
- Auditovat použití klasických virtuálních počítačů
- Auditovat virtuální počítače, které nepoužívají spravované disky

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 správy privilegovaného přístupová práva

Tento podrobný plán vám pomůže omezit a řídit práva privilegovaného přístupu přiřazením čtyři [Azure Policy](../../../policy/overview.md) definice a auditovat externí účty s vlastníkem a/nebo zápis oprávnění a účty s vlastníkem a/nebo oprávnění k zápisu které nemají povolené vícefaktorové ověřování. Azure implementuje řízení přístupu na základě rolí (RBAC) pro správu, kdo má přístup k prostředkům Azure. Tento podrobný plán také přiřadí tři definice zásad Azure a kontrolovat použití ověřování Azure Active Directory pro SQL servery a Service Fabric. Ověřování pomocí Azure Active Directory umožňuje správu oprávnění zjednodušené a centralizované identity management uživatelů databáze a dalším službám společnosti Microsoft. Tento podrobný plán také přiřadí definici zásad Azure kontrolovat použití vlastních pravidel RBAC. Principy, kde jsou implementovat vlastní pravidla RBAC můžete ověřit potřebujete a správnou implementaci vlastní pravidla RBAC jsou náchylné k chybám.

- \[Ve verzi Preview\]: Auditovat účty s oprávněními vlastníka, kteří nejsou v rámci předplatného povolená služba MFA
- \[Ve verzi Preview\]: Auditovat účty s oprávněním pro zápis, které nejsou v rámci předplatného povolená služba MFA
- \[Ve verzi Preview\]: Auditovat externí účty s oprávněními vlastníka na předplatné
- \[Ve verzi Preview\]: Auditování externích účtů s oprávněními pro čtení v předplatném
- Auditovat zřízení správce Azure Active Directory pro SQL server
- Audit využití služby Azure Active Directory pro ověřování klientů v Service Fabric
- Audit využití vlastních pravidel RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>Správa A.9.2.4 informace secret ověřování uživatelů

Tento podrobný plán přiřadí tři [Azure Policy](../../../policy/overview.md) definice auditovat účty, které nemají povolené vícefaktorové ověřování. Multi-Factor authentication pomáhá zabezpečit účty i v případě, že dojde k narušení jednu část ověřovacích informací. Díky monitorování účtů bez povolení ověřování službou Multi-Factor Authentication, můžete určit účty, které mohou být pravděpodobně došlo k narušení. Tento podrobný plán také přiřadí dvěma definicemi zásad Azure, které audit virtuálních počítačů s Linuxem heslo souboru oprávnění pro výstrahy v případě, že máte správně nastavené. Toto nastavení umožňuje využít nápravné akce, která Ujistěte se, že nebudou ohroženy ověřovací data.

- \[Ve verzi Preview\]: Auditovat účty s oprávněními vlastníka, kteří nejsou v rámci předplatného povolená služba MFA
- \[Ve verzi Preview\]: Auditovat účty s oprávněním pro čtení, kteří nejsou v rámci předplatného povolená služba MFA
- \[Ve verzi Preview\]: Auditovat účty s oprávněním pro zápis, které nejsou v rámci předplatného povolená služba MFA
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření auditovat oprávnění k souboru virtuálního počítače s Linuxem hesel
- \[Ve verzi Preview\]: Audit virtuálních počítačů s Linuxem /etc/passwd soubor oprávnění nastavena na hodnotu 0644

## <a name="a925-review-of-user-access-rights"></a>Přehled A.9.2.5 uživatelská přístupová práva

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC), které vám pomůžou spravovat, kdo má přístup k prostředkům v Azure. Pomocí webu Azure portal, můžete zkontrolovat, kdo má přístup k prostředkům Azure a jejich oprávnění. Tento podrobný plán přiřadí čtyři [Azure Policy](../../../policy/overview.md) definice auditovat účty, které by měl být nastaveno jako prioritní ke kontrole, včetně odepsanou účtů a externí účty se zvýšenými oprávněními.

- \[Ve verzi Preview\]: Auditovat zastaralé účty v rámci předplatného
- \[Ve verzi Preview\]: Auditovat zastaralé účty s oprávněními vlastníka na předplatné
- \[Ve verzi Preview\]: Auditovat externí účty s oprávněními vlastníka na předplatné
- \[Ve verzi Preview\]: Auditování externích účtů s oprávněními pro čtení v předplatném

## <a name="a926-removal-or-adjustment-of-access-rights"></a>Odebrání A.9.2.6 nebo úpravu přístupová práva

Azure implementuje [řízení přístupu na základě rolí](../../../../role-based-access-control/overview.md) (RBAC), které vám pomůžou spravovat, kdo má přístup k prostředkům v Azure. Pomocí [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) a RBAC, můžete aktualizovat role uživatele tak, aby odrážely změn v organizaci. Pokud je nepotřebujete, účtů může blokovat přihlášení (nebo odstranit), která okamžitě Odebere přístupová práva k prostředkům Azure. Tento podrobný plán přiřadí dva [Azure Policy](../../../policy/overview.md) definice auditovat odepsanou účet, který by měl být pro odebrání.

- \[Ve verzi Preview\]: Auditovat zastaralé účty v rámci předplatného
- \[Ve verzi Preview\]: Auditovat zastaralé účty s oprávněními vlastníka na předplatné

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 zabezpečené přihlašování postupy

Tento podrobný plán přiřadí tři definic Azure Policy auditovat účty, které nemají povolené vícefaktorové ověřování. Azure Multi-Factor Authentication poskytuje dodatečné zabezpečení vyžadováním druhou formu ověřování a poskytuje silné ověřování. Díky monitorování účtů bez povolení ověřování službou Multi-Factor Authentication, můžete určit účty, které mohou být pravděpodobně došlo k narušení.

- \[Ve verzi Preview\]: Auditovat účty s oprávněními vlastníka, kteří nejsou v rámci předplatného povolená služba MFA
- \[Ve verzi Preview\]: Auditovat účty s oprávněním pro čtení, kteří nejsou v rámci předplatného povolená služba MFA
- \[Ve verzi Preview\]: Auditovat účty s oprávněním pro zápis, které nejsou v rámci předplatného povolená služba MFA

## <a name="a943-password-management-system"></a>Systém správy A.9.4.3 heslo

Tento podrobný plán umožňuje vynucování silných hesel přiřazením 10 [Azure Policy](../../../policy/overview.md) definice, které auditovat virtuální počítače s Windows, který nevynucují minimální sílu a další požadavky na heslo. Sledování virtuálních počítačů v rozporu se zásadami sílu hesel umožňuje provést opravné akce k zajištění, že jsou kompatibilní s zásady hesla pro všechny uživatelské účty virtuálního počítače.

- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření audit virtuálních počítačů Windows vynucuje požadavky na složitost hesla
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření audit virtuálních počítačů Windows maximální stáří hesla 70 dní
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření audit virtuálních počítačů Windows minimální stáří hesla 1 den
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření virtuálního počítače Windows hesel auditovat musí být aspoň 14 znaků.
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření audit virtuálních počítačů Windows by nemělo umožňovat předchozích 24 hesel
- \[Ve verzi Preview\]: Virtuální počítač Windows audit vynucuje požadavky na složitost hesla
- \[Ve verzi Preview\]: Audit virtuálních počítačů Windows maximální stáří hesla 70 dní
- \[Ve verzi Preview\]: Audit virtuálních počítačů Windows minimální stáří hesla 1 den
- \[Ve verzi Preview\]: Audit virtuálních počítačů Windows hesla musí být aspoň 14 znaků.
- \[Ve verzi Preview\]: Virtuální počítač Windows audit by nemělo umožňovat předchozích 24 hesel

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 zásady týkající se použití kryptografie

Tento podrobný plán umožňuje vynucovat zásady týkající se použití ovládacích prvků cryptograph přiřazením 13 [Azure Policy](../../../policy/overview.md) definice, které vynucují konkrétní cryptograph ovládací prvky a auditu použití nastavení slabého šifrování.
Principy, kde vašich prostředků Azure může mít optimální kryptografické konfigurace můžete provést opravné akce k zajištění, že prostředky jsou nakonfigurované v souladu se zásadami zabezpečení vašich informací. Konkrétně zásady přiřazené službou Tento podrobný plán vyžadovat šifrování pro účty blob storage a účty úložiště data lake; vyžadovat transparentní šifrování dat v databázích SQL; Auditovat chybějící šifrování na účty úložiště, databáze SQL, disky virtuálních počítačů a proměnných účtu automation; Auditovat nezabezpečené připojení k účtům úložiště, aplikace Function App, webové aplikace, aplikace API a Redis Cache Auditovat šifrování hesla malý virtuální počítač; a auditovat nešifrovaná komunikace Service Fabric.

- \[Ve verzi Preview\]: Auditovat přístup pouze HTTPS pro aplikaci Function App
- \[Ve verzi Preview\]: Auditovat přístup pouze HTTPS pro webovou aplikaci
- \[Ve verzi Preview\]: Auditovat přístup pouze HTTPS pro aplikaci API
- \[Ve verzi Preview\]: Auditovat chybějící šifrování objektů blob pro účty úložiště
- \[Ve verzi Preview\]: Nasazení virtuálního počítače rozšíření audit virtuálních počítačů Windows neměli ukládat hesla pomocí reverzibilního šifrování
- \[Ve verzi Preview\]: Virtuální počítač Windows audit neměli ukládat hesla pomocí reverzibilního šifrování
- \[Ve verzi Preview\]: Monitorovat nešifrovanou databázi SQL ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorovat nešifrované disky virtuálních počítačů ve službě Azure Security Center
- Auditovat povolení šifrování účtu proměnné služeb automatizace
- Auditovat povolení pouze zabezpečená připojení k mezipaměti Redis
- Auditovat zabezpečený přenos pro účty úložiště
- Auditování nastavení ClusterProtectionLevel vlastnost EncryptAndSign v Service Fabric
- Auditování stavu transparentního šifrování dat

## <a name="a1241-event-logging"></a>Protokolování událostí A.12.4.1

Tento podrobný plán vám pomůže zajistit systémové události jsou protokolovány přiřazením sedm [Azure Policy](../../../policy/overview.md) definice, které auditování nastavení protokolu pro prostředky Azure.
Diagnostické protokoly poskytují přehled o operacích provedených v rámci prostředků Azure.

- \[Ve verzi Preview\]: Nasazení agenta závislostí auditu - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta závislostí v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Monitorovat neauditovanou databázi SQL ve službě Azure Security Center
- Auditování nastavení diagnostiky
- Auditování nastavení úrovně auditování SQL serveru.

## <a name="a1243-administrator-and-operator-logs"></a>Protokoly správce A.12.4.3 and – operátor

Tento podrobný plán vám pomůže zajistit, že systémové události jsou zaznamenána přiřazením sedm definic Azure Policy, které auditování nastavení protokolu pro prostředky Azure. Diagnostické protokoly poskytují přehled o operacích provedených v rámci prostředků Azure.

- \[Ve verzi Preview\]: Nasazení agenta závislostí auditu - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta závislostí v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Monitorovat neauditovanou databázi SQL ve službě Azure Security Center
- Auditování nastavení diagnostiky
- Auditování nastavení úrovně auditování SQL serveru.

## <a name="a1244-clock-synchronization"></a>Synchronizace hodin A.12.4.4

Tento podrobný plán vám pomůže zajistit, že systémové události jsou zaznamenána přiřazením sedm definic Azure Policy, které auditování nastavení protokolu pro prostředky Azure. Protokoly Azure využívají k vytvoření záznamu čas korelační události napříč prostředky synchronizovat interní hodiny.

- \[Ve verzi Preview\]: Nasazení agenta závislostí auditu - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta závislostí v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Auditovat nasazení agenta Log Analytics v VMSS - obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
- \[Ve verzi Preview\]: Monitorovat neauditovanou databázi SQL ve službě Azure Security Center
- Auditování nastavení diagnostiky
- Auditování nastavení úrovně auditování SQL serveru.

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 instalace softwaru na provozní systémy

Adaptivní řízení aplikací je řešení ze služby Azure Security Center, které pomáhá řídit aplikace, které můžete spouštět na vašich virtuálních počítačích v Azure. Tento podrobný plán přiřadí definici rozhraní Azure Policy, která sleduje změny sadu povolených aplikací. Tato funkce umožňuje řídit instalaci softwaru a aplikací na virtuálních počítačích Azure.

- \[Ve verzi Preview\]: Monitorovat možné přidávání aplikací na seznam povolených ve službě Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>Správa A.12.6.1 technické ohrožení zabezpečení

Tento podrobný plán slouží ke správě systému informace o ohrožení zabezpečení přiřazením pět [Azure Policy](../../../policy/overview.md) definice, které monitorovat chybějící aktualizace systému, ohrožení zabezpečení operačního systému, SQL ohrožení zabezpečení a virtuální počítač ohrožení zabezpečení v Azure Security Center. Azure Security Center nabízí možnosti vytváření sestav, které vám umožní mít v reálném čase přehled o stavu zabezpečení nasazených prostředků Azure.

- \[Ve verzi Preview\]: Monitorování chybějící služby Endpoint Protection ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorovat chybějící aktualizace systému ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorování ohrožení zabezpečení operačního systému ve službě Azure Security Center
- \[Ve verzi Preview\]: Sledovat výsledky posouzení ohrožení zabezpečení SQL ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorování ohrožení zabezpečení virtuálních počítačů ve službě Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 omezení týkající se instalace softwaru

Adaptivní řízení aplikací je řešení ze služby Azure Security Center, které pomáhá řídit aplikace, které můžete spouštět na vašich virtuálních počítačích v Azure. Tento podrobný plán přiřadí definici rozhraní Azure Policy, která sleduje změny sadu povolených aplikací. Omezení týkající se instalace softwaru můžete snížit pravděpodobnost, že zavedení chybami zabezpečení softwaru.

- \[Ve verzi Preview\]: Monitorovat možné přidávání aplikací na seznam povolených ve službě Azure Security Center

## <a name="a1311-network-controls"></a>Ovládací prvky A.13.1.1 sítě

Tento podrobný plán umožňuje spravovat a řídit sítě tak, že přiřadíte [Azure Policy](../../../policy/overview.md) definice, která monitoruje skupiny zabezpečení sítě s benevolentními pravidly. Pravidla, která jsou příliš benevolentními může povolit nežádoucí síťový přístup a byste měli zkontrolovat. Tento podrobný plán také přiřadí tři definic Azure Policy, monitorovat nechráněné koncové body, aplikacím a účtům úložiště. Koncové body a aplikace, které nejsou chráněné bránou firewall a účty úložiště s neomezeným přístupem můžete povolit nežádoucí přístup informace obsažené v informačním systému.

- \[Ve verzi Preview\]: Monitorovat benevolentní přístup k síti ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorovat nechráněné koncové body sítě ve službě Azure Security Center
- \[Ve verzi Preview\]: Monitorovat nechráněné webové aplikace ve službě Azure Security Center
- Audit neomezený přístup k síti do účtů úložiště

## <a name="a1321-information-transfer-policies-and-procedures"></a>Zásady přenosu A.13.2.1 informace a postupy

Podrobný plán vám pomůže zajistit přenosu informací pomocí služeb Azure je zabezpečený přiřazením dvě [Azure Policy](../../../policy/overview.md) definice auditovat nezabezpečené připojení k účtům úložiště a Redis Cache.

- Auditovat povolení pouze zabezpečená připojení k mezipaměti Redis
- Auditovat zabezpečený přenos pro účty úložiště

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
