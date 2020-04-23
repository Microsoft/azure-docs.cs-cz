---
title: Přehled zabezpečení v úložišti Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft
description: Zjistěte, jak je Azure Data Lake Storage Storage Gen1 bezpečnější úložiště velkých objemů dat
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 7e987c56c3a125a03e3a90540313ace1f8adf47a
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086568"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Zabezpečení v úložišti datových jezer Azure Gen1

Mnoho podniků využívá analýzu velkých objemů dat pro obchodní přehledy, které jim pomáhají činit chytrá rozhodnutí. Organizace může mít složité a regulované prostředí s rostoucím počtem různorodých uživatelů. Pro podnik je nezbytné zajistit, aby kritická obchodní data byla uložena bezpečněji, se správnou úrovní přístupu udělenou jednotlivým uživatelům. Azure Data Lake Storage Gen1 je navržený tak, aby pomohl splnit tyto požadavky na zabezpečení. V tomto článku se dozvíte o možnostech zabezpečení data lake storage gen1, včetně:

* Authentication
* Autorizace
* Izolace sítě
* Ochrana dat
* Auditování

## <a name="authentication-and-identity-management"></a>Ověřování a správa identit

Ověřování je proces, při kterém je identita uživatele ověřena, když uživatel spolupracuje s Date Lake Storage Gen1 nebo s jakoukoli službou, která se připojuje k datově jezeru Storage Gen1. Pro správu identit a ověřování používá Data Lake Storage Gen1 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), komplexní cloudové řešení správy identit a přístupu, které zjednodušuje správu uživatelů a skupin.

Každé předplatné Azure lze přidružit k instanci služby Azure Active Directory. K vašemu účtu Data Lake Storage Gen1 mají přístup k jenom uživatelům a identitám služeb, které jsou definovány ve vaší službě Azure Active Directory, pomocí portálu Azure Portal, nástrojů příkazového řádku nebo prostřednictvím klientských aplikací, které vaše organizace vytváří pomocí sady SDK úložiště datového jezera. Hlavní výhody použití služby Azure Active Directory jako centralizovaného mechanismu řízení přístupu jsou:

* Zjednodušená správa životního cyklu identity. Identitu uživatele nebo služby (identitu instančního objektu) lze rychle vytvořit a rychle odvolat jednoduchým odstraněním nebo zakázáním účtu v adresáři.
* Vícefaktorové ověřování. [Vícefaktorové ověřování](../active-directory/authentication/multi-factor-authentication.md) poskytuje další vrstvu zabezpečení pro přihlášení uživatelů a transakce.
* Ověřování z libovolného klienta prostřednictvím standardního otevřeného protokolu, například OAuth nebo OpenID.
* Federace s podnikovými adresářovými službami a poskytovateli cloudových identit.

## <a name="authorization-and-access-control"></a>Autorizace a řízení přístupu

Po Azure Active Directory ověří uživatele tak, aby uživatel přístup Data Lake Storage Gen1, autorizace řídí přístupová oprávnění pro úložiště datových jezer Gen1. Data Lake Storage Gen1 odděluje autorizaci pro činnosti související s účtem a daty následujícím způsobem:

* [Řízení přístupu na základě rolí](../role-based-access-control/overview.md) (RBAC) poskytované Azure pro správu účtů
* POSIX ACL pro přístup k datům v úložišti

### <a name="rbac-for-account-management"></a>RBAC pro správu účtů

Čtyři základní role jsou definovány pro data lake storage gen1 ve výchozím nastavení. Role povolují různé operace na účtu Gen1 úložiště datového jezera prostřednictvím portálu Azure, rutin prostředí PowerShell a rozhraní REST API. Role vlastníka a přispěvatele mohou v účtu provádět různé funkce správy. Roli Čtečka můžete přiřadit uživatelům, kteří zobrazují pouze data správy účtu.

![Role RBAC](./media/data-lake-store-security-overview/rbac-roles.png "Role RBAC")

Všimněte si, že i když role jsou přiřazeny pro správu účtu, některé role ovlivňují přístup k datům. K řízení přístupu k operacím, které může uživatel provádět v systému souborů, je třeba použít počet aklů. V následující tabulce je uveden souhrn práv správce a přístupových práv k datům pro výchozí role.

| Role | Práva na správu | Přístupová práva k datům | Vysvětlení |
| --- | --- | --- | --- |
| Nebyla přiřazena žádná role. |Žádná |Řídí se acl |Uživatel nemůže používat portál Azure nebo rutiny Azure PowerShell k procházení Gen1 úložiště datového jezera. Uživatel může používat pouze nástroje příkazového řádku. |
| Vlastník |Všechny |Všechny |Role vlastníka je superuživatel. Tato role může spravovat vše a má úplný přístup k datům. |
| Čtenář |Jen pro čtení |Řídí se acl |Role Čtenář může zobrazit vše, co se týká správy účtu, například který uživatel je přiřazen ke které roli. Role Čtenář nemůže provádět žádné změny. |
| Přispěvatel |Všechny kromě přidání a odebrání rolí |Řídí se acl |Role přispěvatele může spravovat některé aspekty účtu, jako je například nasazení a vytváření a správa výstrah. Role přispěvatele nemůže přidávat nebo odebírat role. |
| Správce přístupu uživatelů |Přidání a odebrání rolí |Řídí se acl |Role Správce přístupu uživatelů může spravovat přístup uživatelů k účtům. |

Pokyny naleznete v tématu [Přiřazení uživatelů nebo skupin zabezpečení k účtům Data Lake Storage Gen1](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Použití aklů pro operace v souborových systémech

Data Lake Storage Gen1 je hierarchický souborový systém, jako je Hadoop Distributed File System (HDFS) a podporuje [POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Řídí čtení (r), zápis (w) a spuštění (x) oprávnění k prostředkům pro roli vlastníka, pro skupinu Owners a pro ostatní uživatele a skupiny. V data lake storage gen1 aklů lze povolit v kořenové složce, v podsložkách a na jednotlivých souborech. Další informace o tom, jak akly fungují v kontextu Data Lake Storage Gen1, najdete [v tématu Řízení přístupu v úložišti datových jezer Gen1](data-lake-store-access-control.md).

Doporučujeme definovat seznamy ACL pro více uživatelů pomocí [skupin zabezpečení](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Přidejte uživatele do skupiny zabezpečení a přiřaďte této skupině zabezpečení a přiřaďte alokaci ACL pro soubor nebo složku. To je užitečné, pokud chcete poskytnout přiřazená oprávnění, protože jste omezeni na maximálně 28 položek pro přiřazená oprávnění. Další informace o tom, jak lépe zabezpečit data uložená v programu Data Lake Storage Gen1 pomocí skupin zabezpečení Azure Active Directory, najdete v tématu [Přiřazení uživatelů nebo skupiny zabezpečení jako seznamů ACL k systému souborů Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

![Přístupová oprávnění k seznamu](./media/data-lake-store-security-overview/adl.acl.2.png "Přístupová oprávnění k seznamu")

## <a name="network-isolation"></a>Izolace sítě

Pomocí funkce Data Lake Storage Gen1 můžete řídit přístup k úložišti dat na úrovni sítě. Můžete vytvořit brány firewall a definovat rozsah IP adres pro důvěryhodné klienty. S rozsahem IP adres se k gen1 úložiště datového jezera 1 mohou připojit pouze klienti, kteří mají IP adresu v rámci definovaného rozsahu.

![Nastavení brány firewall a přístup k IP adrese](./media/data-lake-store-security-overview/firewall-ip-access.png "Nastavení brány firewall a ADRESA IP")

Azure virtuální sítě (VNet) podporují značky služeb pro Data Lake Gen 1. Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit. Další informace najdete v tématu [Přehled značek služeb Azure](../virtual-network/service-tags-overview.md).

## <a name="data-protection"></a>Ochrana dat

Data Lake Storage Gen1 chrání vaše data po celou dobu jejich životního cyklu. Pro přenos dat používá Data Lake Storage Gen1 standardní protokol TLS 1.2 (TLS 1.2) k zabezpečení dat v síti.

![Šifrování v úložišti datového jezera Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Šifrování v úložišti datového jezera Gen1")

Data Lake Storage Gen1 také poskytuje šifrování pro data, která jsou uložena v účtu. Můžete zvolit, aby se vaše data šifrovala, nebo zvolit možnost bez šifrování. Pokud se přihlásíte k šifrování, data uložená v programu Data Lake Storage Gen1 se před uložením na trvalá média zašifrují. V takovém případě Data Lake Storage Gen1 automaticky šifruje data před uchováním a dešifruje data před načtením, takže je zcela transparentní pro klienta přístup k datům. Na straně klienta není nutná žádná změna kódu pro šifrování nebo dešifrování dat.

Pro správu klíčů, Data Lake Storage Gen1 poskytuje dva režimy pro správu hlavníšifrovací klíče (MEKs), které jsou potřebné pro dešifrování všech dat, která je uložena v Datové jezero Storage Gen1. Můžete buď nechat Data Lake Storage Gen1 spravovat MEKs za vás, nebo se rozhodnete zachovat vlastnictví MEKs pomocí účtu Azure Key Vault. Při vytváření účtu Data Lake Storage Gen1 můžete určit režim správy klíčů. Další informace o tom, jak zajistit konfiguraci související s šifrováním, najdete [v tématu Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure Portal](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Protokoly aktivit a diagnostické protokoly

Můžete použít protokoly aktivity nebo diagnostiky v závislosti na tom, zda hledáte protokoly pro aktivity související se správou účtu nebo aktivity související s daty.

* Aktivity související se správou účtů používají azure resource manager api a jsou krejčovské na portálu Azure prostřednictvím protokolů aktivit.
* Aktivity související s daty používají rozhraní API WEBHDFS REST a jsou na webu Azure Portal konsurfacedprostřednictvím diagnostických protokolů.

### <a name="activity-log"></a>Protokol aktivit

Aby byla organizace v souladu s předpisy, může vyžadovat odpovídající auditní stopy aktivit správy účtů, pokud potřebuje prokopat konkrétní incidenty. Data Lake Storage Gen1 má integrované monitorování a zaznamenává všechny aktivity správy účtů.

U záznamů auditu správy účtů zobrazte a zvolte sloupce, které chcete protokolovat. Můžete také exportovat protokoly aktivit do Služby Azure Storage.

![Protokol aktivit](./media/data-lake-store-security-overview/activity-logs.png "Protokol aktivit")

Další informace o práci s protokoly aktivit naleznete v [tématu Zobrazení protokolů aktivit k auditování akcí na zdrojích](../azure-resource-manager/management/view-activity-logs.md).

### <a name="diagnostics-logs"></a>Protokoly diagnostiky

Můžete povolit auditování dat a protokolování diagnostiky na webu Azure portal a odesílat protokoly do účtu úložiště objektů Blob Azure, centra událostí nebo protokolů Azure Monitor.

![Protokoly diagnostiky](./media/data-lake-store-security-overview/diagnostic-logs.png "Protokoly diagnostiky")

Další informace o práci s diagnostickými protokoly s datovým lakem Gen1 najdete [v tématu Přístup k diagnostickým protokolům pro úložiště datových jezer Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Souhrn

Podnikoví zákazníci požadují cloudovou platformu pro analýzu dat, která je bezpečná a snadno použitelná. Data Lake Storage Gen1 je navržen tak, aby pomohl a řešit tyto požadavky prostřednictvím správy identit a ověřování prostřednictvím integrace Azure Active Directory, ACL-založené autorizace, izolace sítě, šifrování dat v tranzitu a v klidovém stavu a auditování.

Pokud chcete vidět nové funkce v Data Lake Storage Gen1, pošlete nám svůj názor ve [fóru Data Lake Storage Gen1 UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Viz také

* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Začínáme s úložištěm datových jezer Gen1](data-lake-store-get-started-portal.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
