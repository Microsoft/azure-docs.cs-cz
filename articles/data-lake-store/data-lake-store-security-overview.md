---
title: Přehled zabezpečení v Azure Data Lake Storage Gen1 | Microsoft Docs
description: Seznamte se s možnostmi zabezpečení Azure Data Lake Storage Gen1, včetně ověřování, autorizace, izolace sítě, ochrany dat a auditování.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 240018381a3139a6378141d78514e43ae469de5d
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146320"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Zabezpečení v Azure Data Lake Storage Gen1

Řada podniků využívá analýzu velkých objemů dat pro Business Insights, která jim usnadňuje rozhodování. Organizace může mít komplexní a regulované prostředí s rostoucím počtem různých uživatelů. Podnik má jistotu, že kritická podniková data se ukládají bezpečněji, se správnou úrovní přístupu uděleným pro jednotlivé uživatele. Azure Data Lake Storage Gen1 je navržena tak, aby pomohla splnit tyto požadavky na zabezpečení. V tomto článku se dozvíte o možnostech zabezpečení Data Lake Storage Gen1, včetně těchto:

* Authentication
* Autorizace
* Izolace sítě
* Ochrana dat
* Auditování

## <a name="authentication-and-identity-management"></a>Ověřování a Správa identit

Ověřování je proces, při kterém je identita uživatele ověřena při interakci uživatele s Data Lake Storage Gen1 nebo pomocí jakékoli služby, která se připojuje k Data Lake Storage Gen1. Pro správu identit a ověřování Data Lake Storage Gen1 využívá [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)komplexní cloudové řešení pro správu identit a přístupu, které zjednodušuje správu uživatelů a skupin.

Každé předplatné Azure je možné přidružit k instanci Azure Active Directory. K účtu Data Lake Storage Gen1 můžou přistupovat jenom identity uživatelů a služeb, které jsou definované ve službě Azure Active Directory, pomocí nástrojů pro Azure Portal, nástrojů příkazového řádku nebo prostřednictvím klientských aplikací, které vaše organizace sestaví pomocí sady Data Lake Storage Gen1 SDK. Mezi klíčové výhody použití Azure Active Directory jako centralizovaného mechanismu řízení přístupu patří:

* Zjednodušená správa životního cyklu identit. Identitu uživatele nebo služby (identitu instančního objektu) můžete rychle vytvořit a rychle odvolat pouhým odstraněním nebo zakázáním účtu v adresáři.
* Multi-Factor Authentication. [Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) poskytuje další úroveň zabezpečení pro přihlášení a transakce uživatelů.
* Ověřování od libovolného klienta prostřednictvím standardního otevřeného protokolu, jako je například OAuth nebo OpenID.
* Federace s podnikovými adresářovými službami a poskytovateli cloudových identit.

## <a name="authorization-and-access-control"></a>Autorizace a řízení přístupu

Po Azure Active Directory ověří uživatele, aby uživatel měl přístup k Data Lake Storage Gen1, řízení autorizací přístup k oprávněním pro Data Lake Storage Gen1. Data Lake Storage Gen1 odděluje autorizaci pro aktivity vztahující se k účtu a k datům následujícím způsobem:

* [Řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md) pro správu účtů
* Seznam ACL POSIX pro přístup k datům v úložišti

### <a name="azure-rbac-for-account-management"></a>Azure RBAC pro správu účtů

Ve výchozím nastavení jsou definovány čtyři základní role Data Lake Storage Gen1. Role povolují různé operace s účtem Data Lake Storage Gen1 prostřednictvím Azure Portal, rutin prostředí PowerShell a rozhraní REST API. Role vlastníka a přispěvatele můžou na účtu provádět různé funkce správy. Roli Čtenář můžete přiřadit uživatelům, kteří zobrazují jenom data správy účtů.

![Role Azure](./media/data-lake-store-security-overview/rbac-roles.png "Role Azure")

Mějte na paměti, že i když se role přiřazují pro správu účtů, některé role mají vliv na přístup k datům. Chcete-li řídit přístup k operacím, které může uživatel provádět v systému souborů, je nutné použít seznamy řízení přístupu (ACL). Následující tabulka obsahuje souhrn práv ke správě a oprávnění k přístupu k datům pro výchozí role.

| Role | Práva pro správu | Přístupová práva k datům | Vysvětlení |
| --- | --- | --- | --- |
| Není přiřazená žádná role. |Žádné |Řídí se ACL |Uživatel nemůže pomocí rutin Azure Portal ani Azure PowerShell Procházet Data Lake Storage Gen1. Uživatel může použít jenom nástroje příkazového řádku. |
| Vlastník |Vše |Vše |Role vlastníka je uživatel. Tato role může spravovat vše a má úplný přístup k datům. |
| Čtenář |Jen pro čtení |Řídí se ACL |Role čtenář si může zobrazit všechno, co se týče správy účtů, například k tomu, ke kterému uživateli je přiřazena role. Role čtenáře nemůže provádět žádné změny. |
| Přispěvatel |Všechny kromě rolí přidat a odebrat |Řídí se ACL |Role přispěvatele může spravovat některé aspekty účtu, například nasazení a vytváření a správu výstrah. Role přispěvatele nemůže přidávat ani odebírat role. |
| Správce uživatelských přístupů |Přidání a odebrání rolí |Řídí se ACL |Role správce přístupu uživatelů může spravovat přístup uživatelů k účtům. |

Pokyny najdete v tématu [přiřazení uživatelů nebo skupin zabezpečení pro data Lake Storage Gen1 účtů](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Používání seznamů ACL pro operace se systémy souborů

Data Lake Storage Gen1 je hierarchický systém souborů jako Hadoop systém souborů DFS (Distributed File System) (HDFS) a podporuje [seznamy ACL pro POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Ovládá oprávnění číst (r), zapsat (w) a spouštět (x) pro prostředky pro roli vlastníka, pro skupinu vlastníci a pro jiné uživatele a skupiny. V Data Lake Storage Gen1 mohou být seznamy ACL povoleny v kořenové složce, v podsložkách a v jednotlivých souborech. Další informace o tom, jak seznamy ACL fungují v kontextu Data Lake Storage Gen1, najdete v tématu [řízení přístupu v Data Lake Storage Gen1](data-lake-store-access-control.md).

Pro více uživatelů Doporučujeme definovat seznamy ACL pomocí [skupin zabezpečení](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Přidejte uživatele do skupiny zabezpečení a potom přiřaďte seznamy ACL pro soubor nebo složku do této skupiny zabezpečení. To je užitečné, když chcete poskytnout přiřazená oprávnění, protože pro přidělená oprávnění máte omezení maximálně 28 položek. Další informace o tom, jak lépe zabezpečit data uložená v Data Lake Storage Gen1 pomocí skupin zabezpečení Azure Active Directory, najdete v tématu [přiřazení uživatelů nebo skupiny zabezpečení jako seznamů ACL do systému souborů Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

![Vypsat přístupová oprávnění](./media/data-lake-store-security-overview/adl.acl.2.png "Vypsat přístupová oprávnění")

## <a name="network-isolation"></a>Izolace sítě

Použijte Data Lake Storage Gen1 k usnadnění řízení přístupu k úložišti dat na úrovni sítě. Můžete navázat brány firewall a definovat rozsah IP adres pro důvěryhodné klienty. S rozsahem IP adres se můžou Data Lake Storage Gen1 připojit jenom klienti, kteří mají IP adresu v rámci definovaného rozsahu.

![Nastavení brány firewall a přístup k protokolu IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Nastavení a IP adresa brány firewall")

Značky služby Azure Virtual Networks (VNet) pro Data Lake Gen 1. Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny. Další informace najdete v tématu [Přehled značek služeb Azure](../virtual-network/service-tags-overview.md).

## <a name="data-protection"></a>Ochrana dat

Data Lake Storage Gen1 chrání vaše data během svého životního cyklu. Pro přenos dat při přenosu Data Lake Storage Gen1 používá standardní protokol TLS 1,2 (Transport Layer Security) k zabezpečení dat v síti.

![Šifrování v Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Šifrování v Data Lake Storage Gen1")

Data Lake Storage Gen1 taky poskytuje šifrování pro data, která jsou uložená v účtu. Můžete zvolit, aby se vaše data šifrovala, nebo zvolit možnost bez šifrování. Pokud se rozhodnete pro šifrování, data uložená v Data Lake Storage Gen1 se šifrují před uložením na trvalé médium. V takovém případě Data Lake Storage Gen1 data automaticky šifruje před uložením a dešifruje data před jejich načtením, takže je zcela transparentní pro přístup klienta k datům. Na straně klienta není nutná žádná změna kódu pro šifrování/dešifrování dat.

Pro správu klíčů poskytuje Data Lake Storage Gen1 dva režimy pro správu hlavních šifrovacích klíčů (hlavních šifrovacích klíčů), které jsou potřeba k dešifrování dat uložených v Data Lake Storage Gen1. Můžete buď nechat Data Lake Storage Gen1 spravovat hlavních šifrovacích klíčů za vás, nebo se rozhodnout zachovat vlastnictví hlavních šifrovacích klíčů pomocí účtu Azure Key Vault. Při vytváření Data Lake Storage Gen1 účtu zadáte režim správy klíčů. Další informace o tom, jak poskytnout konfiguraci související s šifrováním, najdete v tématu Začínáme [s Azure Data Lake Storage Gen1 pomocí webu Azure Portal](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Protokoly aktivit a diagnostické protokoly

Protokoly aktivit nebo diagnostiky můžete použít v závislosti na tom, jestli hledáte protokoly pro aktivity související se správou účtů nebo aktivity související s daty.

* Aktivity související se správou účtů používají Azure Resource Manager rozhraní API a jsou v Azure Portal v protokolech aktivit.
* Aktivity související s daty používají rozhraní REST API WebHDFS a jsou umístěny v Azure Portal prostřednictvím diagnostických protokolů.

### <a name="activity-log"></a>Protokol aktivit

Aby mohla organizace dodržovat předpisy, může vyžadovat, aby si v případě potřeby dig do konkrétních incidentů odpovídající kontrolní záznamy aktivit správy účtů. Data Lake Storage Gen1 má integrované monitorování a zaznamenává všechny aktivity správy účtů.

V části audit správy účtů si prohlédněte a vyberte sloupce, které chcete protokolovat. Protokoly aktivit můžete také exportovat do Azure Storage.

![Protokol aktivit](./media/data-lake-store-security-overview/activity-logs.png "Protokol aktivit")

Další informace o práci s protokoly aktivit najdete v tématu [zobrazení protokolů aktivit pro audit akcí u prostředků](../azure-resource-manager/management/view-activity-logs.md).

### <a name="diagnostics-logs"></a>Protokoly diagnostiky

Můžete povolit auditování přístupu k datům a diagnostické protokolování v Azure Portal a odesílat protokoly do účtu služby Azure Blob Storage, centra událostí nebo protokolů Azure Monitor.

![Protokoly diagnostiky](./media/data-lake-store-security-overview/diagnostic-logs.png "Protokoly diagnostiky")

Další informace o práci s diagnostickými protokoly pomocí Data Lake Storage Gen1 najdete v tématu [přístup k diagnostickým protokolům pro data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Shrnutí

Podnikoví zákazníci požadují cloudovou platformu pro analýzu dat, která je zabezpečená a snadno použitelná. Data Lake Storage Gen1 je navržená tak, aby vám pomohla vyřešit tyto požadavky prostřednictvím správy identit a ověřování prostřednictvím Azure Active Directory integrace, ověřování na základě seznamů ACL, izolace sítě, šifrování dat při přenosu a v klidovém provozu a auditování.

Pokud chcete zobrazit nové funkce v Data Lake Storage Gen1, pošlete nám svůj názor na [Data Lake Storage Gen1 Fórum UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Viz také

* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Začínáme s Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)