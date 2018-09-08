---
title: Přehled zabezpečení v Data Lake Store | Dokumentace Microsoftu
description: Vysvětlení, jak Azure Data Lake Store je bezpečnější úložišti velkých objemů dat
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 4ecc94f4ab5e9091db1705e99d4a5df6abbaf350
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161080"
---
# <a name="security-in-azure-data-lake-store"></a>Zabezpečení v Azure Data Lake Store
Řada podniků se využívat analýzy velkých objemů dat pro obchodní přehledy, aby to pomohl ostatním inteligentní rozhodování. Organizace může mít složité a regulované prostředí s rostoucí počet odlišných uživatelů. Je důležité pro podnik, abyste měli jistotu, že životně důležitá podniková data uložená bezpečněji, se správnou úroveň přístupu k jednotlivým uživatelům. Azure Data Lake Store je účelem je pomoci splnit tyto požadavky na zabezpečení. V tomto článku najdete další informace o možnostech zabezpečení služby Data Lake Store, včetně:

* Authentication
* Autorizace
* Izolace sítě
* Ochrana dat
* Auditování

## <a name="authentication-and-identity-management"></a>Ověřování a správě identit.
Ověřování je proces, podle kterého se ověřit identitu uživatele při interakci uživatele s Data Lake Store nebo se všemi službami, který se připojuje k Data Lake Store. Ke správě identit a ověřování Data Lake Store používá [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), komplexní identit a přístupu cloudové řešení pro správu, která zjednodušuje správu uživatelů a skupin.

Každé předplatné Azure může být přidružena k instanci služby Azure Active Directory. Pouze uživatelé a služby identit, které jsou definovány ve službě Azure Active Directory můžete přístup k účtu Data Lake Store pomocí webu Azure portal, nástrojů příkazového řádku, nebo prostřednictvím klientských aplikací vaší organizace sestavení pomocí Azure Data Lake Store SDK. Jsou klíčové výhody používání služby Azure Active Directory jako vhodný mechanismus řízení centralizovaný přístup:

* Zjednodušená správa životního cyklu identit. Identita uživatele nebo službu (objektu zabezpečení identity služby) se dají rychle vytvářet a rychle odvolat jednoduše odstranění nebo zakázání účtů v adresáři.
* Ověřování službou Multi-Factor Authentication. [Ověřování službou Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) poskytuje další úroveň zabezpečení pro uživatelská přihlášení a transakce.
* Ověřování z libovolného klienta přes standardní otevřený protokol, jako jsou OAuth nebo OpenID.
* Federace se službou enterprise adresářových služeb a poskytovatelů cloudových identit.

## <a name="authorization-and-access-control"></a>Řízení přístupu a autorizace
Poté, co je Azure Active Directory ověří uživatele, aby uživatel přístup k Azure Data Lake Store, prvků pro ověřování přístupová oprávnění pro Data Lake Store. Data Lake Store odděluje autorizace pro aktivity související s účtem a související data následujícím způsobem:

* [Řízení přístupu na základě rolí](../role-based-access-control/overview.md) (RBAC) poskytovaný platformou Azure pro správu účtu
* POSIX ACL pro přístup k datům v úložišti

### <a name="rbac-for-account-management"></a>RBAC pro správu účtu
Čtyři základní role jsou definovány pro Data Lake Store ve výchozím nastavení. Role povolit různé operace v účtu Data Lake Store pomocí webu Azure portal, rutin prostředí PowerShell a rozhraní REST API. Role vlastník a Přispěvatel můžete provádět řadu funkcí správy v účtu. Můžete přiřadit role Čtenář pro uživatele, kteří zobrazit pouze data správy účtu.

![Role RBAC](./media/data-lake-store-security-overview/rbac-roles.png "role RBAC")

Všimněte si, že i když se přiřadí role pro správu účtu, některé role mít vliv na přístup k datům. Budete muset použít seznamy ACL pro řízení přístupu k operacím, které může uživatel provést v systému souborů. Následující tabulka uvádí přehled management rights a přístupová práva pro výchozí role.

| Role | Práva pro správu | Přístupová práva | Vysvětlení |
| --- | --- | --- | --- |
| Žádné role přiřazená |Žádný |Řídí seznamu ACL |Uživatele nelze použít na webu Azure portal nebo rutin Azure Powershellu k procházení Data Lake Store. Uživatel může použít jenom nástroje příkazového řádku. |
| Vlastník |Vše |Vše |Role vlastníka je superuživatele. Této roli můžou spravovat všechno a má úplný přístup k datům. |
| Čtenář |Jen pro čtení |Řídí seznamu ACL |Role čtenáře může vše zobrazit týkající se správy účtu, například u kterých je uživatel přiřazený k jakou roli. Role Čtenář nemůže provádět žádné změny. |
| Přispěvatel |Všechny s výjimkou přidání a odebrání role |Řídí seznamu ACL |Role Přispěvatel může spravovat některé aspekty účtu, jako je nasazení a vytváření a Správa výstrah. Role Přispěvatel nelze přidat nebo odebrat role. |
| Správce přístupu uživatelů |Přidání a odebrání role |Řídí seznamu ACL |Role správce přístupu uživatelů můžete spravovat přístup uživatelů k účtům. |

Pokyny najdete v tématu [přiřadit uživatelům nebo skupinám zabezpečení do účtů Data Lake Store](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Použití seznamů ACL pro operace v systémech souborů
Data Lake Store je systém souborů hierarchické jako soubor systému HDFS (Hadoop Distributed) a podporuje [seznamech ACL POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Určuje číst (r), zapisovat (w) a provést (x) oprávnění k prostředkům pro roli vlastníka, vlastníci skupiny a pro ostatní uživatele a skupiny. Seznamy ACL v Data Lake Store, je možné povolit pro kořenovou složku, podsložky a jednotlivé soubory. Další informace o fungování seznamů řízení přístupu v souvislosti s Data Lake Storem najdete v tématu [Řízení přístupu v Data Lake Storu](data-lake-store-access-control.md).

Doporučujeme definovat seznamy ACL pro více uživatelů pomocí [skupiny zabezpečení](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Přidat uživatele do skupiny zabezpečení a pak přiřaďte tuto skupinu zabezpečení se seznamy ACL pro soubor nebo složku. To je užitečné, pokud byste chtěli poskytnout přiřazená oprávnění, protože pro vás platí omezení na maximální počet 28 položky pro přiřazená oprávnění. Další informace o tom, jak lépe zabezpečit data uložená v Data Lake Store s použitím skupin zabezpečení Azure Active Directory najdete v tématu [přiřazení uživatelů nebo skupin zabezpečení jako seznamů ACL do systému souborů Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions).

![Seznam oprávnění k přístupu](./media/data-lake-store-security-overview/adl.acl.2.png "seznam oprávnění k přístupu")

## <a name="network-isolation"></a>Izolace sítě
Použití Data Lake Store k řízení přístupu pomáhají s vaším úložištěm dat na úrovni sítě. Můžete určit brány firewall a definovat rozsah IP adres pro klienty důvěryhodné. S rozsahem IP adres můžete připojit pouze klienti, kteří mají IP adresu v definovaném rozsahu k Data Lake Store.

![Přístup k nastavení brány firewall a IP](./media/data-lake-store-security-overview/firewall-ip-access.png "brány Firewall, nastavení a IP adresy")

## <a name="data-protection"></a>Ochrana dat
Azure Data Lake Store chrání vaše data celého životního cyklu. Pro data přenášená Data Lake Store používá standardní průmyslový protokol zabezpečení transportní vrstvy (TLS 1.2) k zabezpečení dat v síti.

![Šifrování ve službě Data Lake Store](./media/data-lake-store-security-overview/adls-encryption.png "šifrování ve službě Data Lake Store")

Data Lake Store také zajišťuje šifrování dat, která jsou uložená v účtu. Můžete zvolit, aby se vaše data šifrovala, nebo zvolit možnost bez šifrování. Pokud se připojíte k šifrování, je před uložením na trvalé médium šifrovaná data uložená v Data Lake Store. V takovém případě se Data Lake Store automaticky šifruje data před uložením a dešifruje před načtením, takže všechno je dokonale transparentní klientovi přístupu k datům. Není žádná změna kódu, třeba šifrování/dešifrování dat na straně klienta.

Pro správu klíčů Data Lake Store nabízí dva režimy pro správu vašich hlavních šifrovacích klíčů (MEK), které jsou požadovány pro dešifrování všechna data, která je uložena v Data Lake Store. Můžete buď nechat Data Lake Store správu hlavních šifrovacích klíčů pro vás nebo zachovejte vlastnictví hlavních šifrovacích klíčů pomocí svého účtu služby Azure Key Vault. Určení režimu správy klíčů při vytváření účtu Data Lake Store. Další informace o tom, jak provést konfiguraci související se šifrováním, najdete v tématu [Začínáme s Azure Data Lake Storem pomocí webu Azure Portal](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Aktivita a diagnostické protokoly
Můžete použít aktivitu nebo diagnostické protokoly, v závislosti na tom, jestli hledáte v protokolech aktivit souvisejících se správou účtu nebo aktivit souvisejících s daty.

* Aktivity souvisejících se správou účtu pomocí rozhraní API Azure Resource Manageru a zobrazují na webu Azure Portal prostřednictvím protokolů aktivit.
* Aktivity související s daty pomocí rozhraní REST API WebHDFS a zobrazují na webu Azure Portal prostřednictvím diagnostické protokoly.

### <a name="activity-log"></a>Protokol aktivit
V souladu s předpisy, organizace může vyžadovat záznamy pro audit odpovídající účet správy aktivit Pokud potřebuje Ponořte se do konkrétní incidenty. Data Lake Store je integrované monitorování a protokoluje všechny aktivity správy účtu.

Pro záznamy pro audit správy účtu zobrazit a vybrat sloupce, které chcete se přihlásit. Také můžete exportovat protokoly aktivit do služby Azure Storage.

![Protokol aktivit](./media/data-lake-store-security-overview/activity-logs.png "protokolu aktivit")

Další informace o využití protokolů aktivit najdete v tématu [zobrazení protokolů aktivit pro auditování akcí u prostředků](../azure-resource-manager/resource-group-audit.md).

### <a name="diagnostics-logs"></a>Protokoly diagnostiky
Můžete povolit auditování přístupu k data a diagnostické protokolování na portálu Azure portal a odesílání protokolů do účtu služby Azure Blob storage, se v Centru událostí nebo Log Analytics.

![Protokoly diagnostiky](./media/data-lake-store-security-overview/diagnostic-logs.png "diagnostické protokoly")

Další informace o práci se diagnostické protokoly s Azure Data Lake Store najdete v tématu [zobrazení diagnostických protokolů pro Data Lake Store](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Souhrn
Podnikoví zákazníci potřebují cloudové platformě pro analýzu dat, které jsou zabezpečené a snadno se používá. Azure Data Lake Store je navržená tak, aby vám budou snadněji řešit tyto požadavky prostřednictvím správy identit a ověřování prostřednictvím integrace Azure Active Directory, ověřování na základě seznamu ACL, izolaci sítě, šifrování dat během přenosu a v klidovém stavu a auditování.

Pokud chcete zobrazit nové funkce v Data Lake Store, pošlete nám svůj názor [fórum Data Lake Store UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled Azure Data Lake Store](data-lake-store-overview.md)
* [Začínáme s Data Lake Store](data-lake-store-get-started-portal.md)
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)

