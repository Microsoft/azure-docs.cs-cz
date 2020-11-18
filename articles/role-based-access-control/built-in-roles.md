---
title: Předdefinované role Azure – Azure RBAC
description: Tento článek popisuje předdefinované role Azure pro řízení přístupu na základě role (Azure RBAC). Zobrazuje seznam akcí, NotActions, dataactions a NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 11/03/2020
ms.custom: generated
ms.openlocfilehash: 2ffa5d65675b8fe6eeecbcc71c49d7aa8ebb7eaf
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657767"
---
# <a name="azure-built-in-roles"></a>Předdefinované role v Azure

[Řízení přístupu na základě role v Azure (Azure RBAC)](overview.md) má několik předdefinovaných rolí Azure, které můžete přiřadit uživatelům, skupinám, objektům služby a spravovaným identitám. Přiřazení rolí představují způsob, jakým ovládáte přístup k prostředkům Azure. Pokud předdefinované role nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní [vlastní role Azure](custom-roles.md).

V tomto článku jsou uvedené předdefinované role Azure, které se vždy vyvíjející. K získání nejnovějších rolí použijte příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) nebo [AZ role definition list](/cli/azure/role/definition#az-role-definition-list). Pokud hledáte role správce pro Azure Active Directory (Azure AD), přečtěte si téma [oprávnění role správce v Azure Active Directory](../active-directory/roles/permissions-reference.md).

Následující tabulka uvádí stručný popis a jedinečné ID každé předdefinované role. Kliknutím na název role zobrazíte seznam `Actions` ,, a `NotActions` `DataActions` `NotDataActions` pro každou roli. Informace o tom, co tyto akce znamenají a jak se vztahují na datové roviny pro správu a data, najdete v tématu [vysvětlení definic rolí Azure](role-definitions.md).

## <a name="all"></a>Vše

> [!div class="mx-tableFixed"]
> | Předdefinovaná role | Popis | ID |
> | --- | --- | --- |
> | **Obecné** |  |  |
> | [Přispěvatel](#contributor) | Udělí úplný přístup ke správě všech prostředků, ale neumožňuje přiřadit role v Azure RBAC. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Vlastník](#owner) | Udělí úplný přístup ke správě všech prostředků, včetně možnosti přiřadit role v Azure RBAC. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Čtenář](#reader) | Zobrazit všechny prostředky, ale neumožňuje provádět žádné změny. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Správce uživatelského přístupu](#user-access-administrator) | Umožňuje spravovat přístup uživatelů k prostředkům Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Výpočetní služby** |  |  |
> | [Přispěvatel klasických virtuálních počítačů](#classic-virtual-machine-contributor) | Umožňuje správu klasických virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým se připojuje. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Přihlášení správce virtuálního počítače](#virtual-machine-administrator-login) | Zobrazit Virtual Machines na portálu a přihlásit se jako správce | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Přispěvatel virtuálních počítačů](#virtual-machine-contributor) | Umožňuje správu virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým se připojuje. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Přihlášení uživatele virtuálního počítače](#virtual-machine-user-login) | Zobrazte Virtual Machines na portálu a přihlaste se jako běžný uživatel. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Sítě** |  |  |
> | [Přispěvatel koncového bodu CDN](#cdn-endpoint-contributor) | Může spravovat koncové body CDN, ale nemůže udělit přístup jiným uživatelům. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Čtečka koncového bodu CDN](#cdn-endpoint-reader) | Může zobrazovat koncové body CDN, ale nemůže provádět změny. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Přispěvatel profilu CDN](#cdn-profile-contributor) | Může spravovat profily CDN a jejich koncové body, ale nemůže udělit přístup jiným uživatelům. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Čtečka profilů CDN](#cdn-profile-reader) | Může zobrazit profily CDN a jejich koncové body, ale nemůže provádět změny. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Přispěvatel klasických sítí](#classic-network-contributor) | Umožňuje správu klasických sítí, ale ne přístup k nim. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Přispěvatel zóny DNS](#dns-zone-contributor) | Umožňuje spravovat zóny a sady záznamů DNS v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Přispěvatel sítě](#network-contributor) | Umožňuje spravovat sítě, ale ne přístup k nim. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Přispěvatel Privátní DNS zóny](#private-dns-zone-contributor) | Umožňuje spravovat soukromé prostředky zóny DNS, ale ne virtuální sítě, ke kterým jsou připojené. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Přispěvatel Traffic Manager](#traffic-manager-contributor) | Umožňuje správu profilů Traffic Manager, ale neumožňuje řídit, kdo k nim má přístup. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Přispěvatel avere](#avere-contributor) | Může vytvořit a spravovat cluster avere vFXT. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere – operátor](#avere-operator) | Používá cluster avere vFXT ke správě clusteru. | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Přispěvatel zálohování](#backup-contributor) | Umožňuje správu služby zálohování, ale nemůže vytvářet trezory a udělovat přístup jiným uživatelům. | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operátor záloh](#backup-operator) | Umožňuje správu zálohovacích služeb s výjimkou odebrání zálohování, vytváření trezoru a poskytování přístupu jiným uživatelům. | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Čtečka zálohování](#backup-reader) | Může zobrazovat služby zálohování, ale nemůže provádět změny. | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Přispěvatel klasických účtů úložiště](#classic-storage-account-contributor) | Umožňuje správu klasických účtů úložiště, ale ne přístup k nim. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Role služby operátora klíče klasického účtu úložiště](#classic-storage-account-key-operator-service-role) | Operátoři klíčů pro klasický účet úložiště můžou vypisovat a znovu generovat klíče na klasických účtech úložiště. | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Přispěvatel Data Box](#data-box-contributor) | Umožňuje spravovat všechno, co služba Data Box, s výjimkou poskytnutí přístupu jiným uživatelům. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Čtecí modul Data Box](#data-box-reader) | Umožňuje správu Data Box služby s výjimkou vytváření pořadí nebo úprav podrobností objednávky a udělení přístupu jiným uživatelům. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Vývojář Data Lake Analytics](#data-lake-analytics-developer) | Umožňuje odesílat, monitorovat a spravovat vlastní úlohy, ale neumožňuje vytvářet ani odstraňovat Data Lake Analytics účty. | 47b7735b-770e-4598-A7DA-8b91488b4c88 |
> | [Čtenář a přístup k datům](#reader-and-data-access) | Umožňuje zobrazit vše, ale neumožní vám odstranit ani vytvořit účet úložiště nebo obsažený prostředek. Umožní taky přístup pro čtení a zápis ke všem datům, která jsou obsažená v účtu úložiště, prostřednictvím přístupu k klíčům účtu úložiště. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Přispěvatel účtů úložiště](#storage-account-contributor) | Umožňuje správu účtů úložiště. Poskytuje přístup k klíči účtu, který se dá použít pro přístup k datům přes autorizaci pomocí sdíleného klíče. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Role služby operátora klíče účtu úložiště](#storage-account-key-operator-service-role) | Povoluje výpis a opětovné generování přístupových klíčů účtu úložiště. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Přispěvatel dat v objektech blob služby Storage](#storage-blob-data-contributor) | Čtení, zápis a odstraňování kontejnerů Azure Storage a objektů BLOB. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Vlastník dat v objektech blob služby Storage](#storage-blob-data-owner) | Poskytuje úplný přístup k Azure Storage kontejnerů a dat objektů blob, včetně přiřazování řízení přístupu k POSIX. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Čtenář dat v objektech blob služby Storage](#storage-blob-data-reader) | Čtení a výpis Azure Storage kontejnerů a objektů BLOB. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegování objektu BLOB úložiště](#storage-blob-delegator) | Získejte klíč pro delegování uživatelů, který se pak dá použít k vytvoření sdíleného přístupového podpisu pro kontejner nebo objekt blob, který je podepsaný pomocí přihlašovacích údajů Azure AD. Další informace najdete v tématu [Vytvoření SAS pro delegování uživatelů](/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Přispěvatel sdílené složky SMB dat souboru úložiště](#storage-file-data-smb-share-contributor) | Umožňuje čtení, zápis a odstraňování přístupu k souborům nebo adresářům ve sdílených složkách Azure. Tato role nemá na souborových serverech se systémem Windows žádný předdefinovaný ekvivalent. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Přispěvatel sdílené složky SMB dat souboru úložiště s vyššími oprávněními](#storage-file-data-smb-share-elevated-contributor) | Umožňuje číst, zapisovat, odstraňovat a upravovat seznamy ACL pro soubory nebo adresáře ve sdílených složkách Azure. Tato role je ekvivalentem seznamu ACL sdílené složky pro změnu na souborových serverech systému Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Čtenář sdílené složky SMB dat souboru úložiště](#storage-file-data-smb-share-reader) | Umožňuje přístup pro čtení souborů nebo adresářů ve sdílených složkách Azure. Tato role je ekvivalentem seznamu ACL pro sdílení souborů na souborových serverech systému Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Přispěvatel dat fronty úložiště](#storage-queue-data-contributor) | Čtení, zápis a odstraňování front Azure Storage a zpráv fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Procesor zpráv s daty ve frontě úložiště](#storage-queue-data-message-processor) | Prohlížet, načítat a odstraňovat zprávy z Azure Storage fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Odesílatel zprávy s daty ve frontě úložiště](#storage-queue-data-message-sender) | Přidejte zprávy do fronty Azure Storage. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Čtečka dat fronty úložiště](#storage-queue-data-reader) | Čtení a výpis Azure Storage front a zpráv fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Čtečka dat Azure Maps](#azure-maps-data-reader) | Udělí přístup ke čtení dat souvisejících s mapou z účtu Azure Maps. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Přispěvatel Search Service](#search-service-contributor) | Umožňuje spravovat služby vyhledávání, ale ne přístup k nim. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Čtečka AccessKey signálu](#signalr-accesskey-reader) | Čtení klíčů služby signalizace | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [Server aplikace signaler (Preview)](#signalr-app-server-preview) | Umožňuje službě signalizace přístupového serveru aplikace pomocí možností ověřování AAD. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [Přispěvatel signálu](#signalr-contributor) | Vytváření, čtení, aktualizace a odstraňování prostředků služby signalizace | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [Přispěvatel bez serveru v Signal (Preview)](#signalr-serverless-contributor-preview) | Umožňuje službě App Access v režimu bez serveru s možnostmi ověřování AAD. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [Vlastník služby signaler (Preview)](#signalr-service-owner-preview) | Úplný přístup k rozhraním REST API služby Azure Signal | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [Čtečka služby signaler (Preview)](#signalr-service-reader-preview) | Přístup jen pro čtení k rozhraním REST API služby Azure Signal | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Přispěvatel webového plánu](#web-plan-contributor) | Umožňuje spravovat webové plány pro weby, ale ne přístup k nim. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Přispěvatel webu](#website-contributor) | Umožňuje spravovat weby (nikoli webové plány), ale ne přístup k nim. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containers** |  |  |
> | [AcrDelete](#acrdelete) | Odstranit ACR | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | podpis ACR image | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ACR pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | nabízení ACR | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | čtečka dat karantény ACR | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | zapisovač dat karantény ACR | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Role Správce clusteru služby Azure Kubernetes](#azure-kubernetes-service-cluster-admin-role) | Vypíše akci přihlašovacích údajů správce clusteru. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Role uživatele clusteru služby Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Vypíše akci přihlašovacích údajů uživatele clusteru. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Role přispěvatele služby Azure Kubernetes](#azure-kubernetes-service-contributor-role) | Uděluje přístup ke čtení a zápisu clusterů služby Azure Kubernetes. | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Správce RBAC služby Azure Kubernetes](#azure-kubernetes-service-rbac-admin) | Umožňuje spravovat všechny prostředky v rámci clusteru nebo oboru názvů, s výjimkou kvót prostředků a obory názvů pro aktualizaci nebo odstranění. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Správce clusteru RBAC služby Azure Kubernetes](#azure-kubernetes-service-rbac-cluster-admin) | Umožňuje správu všech prostředků v clusteru. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Čtečka RBAC služby Azure Kubernetes](#azure-kubernetes-service-rbac-reader) | Povoluje přístup jen pro čtení k zobrazení většiny objektů v oboru názvů. Nepovoluje zobrazování rolí nebo vazeb rolí. Tato role nepovoluje zobrazení tajných kódů, protože čtení obsahu tajných kódů umožňuje přístup k přihlašovacím údajům ServiceAccount v oboru názvů, což by mohlo povolit přístup k rozhraní API jako libovolný ServiceAccount v oboru názvů (forma eskalace oprávnění). Použití této role v oboru clusteru umožní přístup napříč všemi obory názvů. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Zapisovač RBAC služby Azure Kubernetes](#azure-kubernetes-service-rbac-writer) | Povoluje přístup pro čtení a zápis většiny objektů v oboru názvů. Tato role nepovoluje zobrazování ani úpravy rolí nebo vazeb rolí. Tato role však umožňuje přístup k tajným klíčům a spuštění lusků jako libovolných ServiceAccount v oboru názvů, takže se dá použít k získání úrovní přístupu rozhraní API všech ServiceAccount v oboru názvů. Použití této role v oboru clusteru umožní přístup napříč všemi obory názvů. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Databáze** |  |  |
> | [Role čtečky účtu Cosmos DB](#cosmos-db-account-reader-role) | Může číst data Azure Cosmos DB účtu. Správa účtů Azure Cosmos DB najdete v tématu [Přispěvatel účtu DocumentDB](#documentdb-account-contributor) . | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operátor Cosmos DB](#cosmos-db-operator) | Umožňuje správu účtů Azure Cosmos DB, ale ne přístup k datům v nich. Zabraňuje přístup k klíčům účtu a připojovacím řetězcům. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Může odeslat žádost o obnovení pro Cosmos DB databázi nebo kontejner pro účet. | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Přispěvatel účtu DocumentDB](#documentdb-account-contributor) | Může spravovat účty Azure Cosmos DB. Azure Cosmos DB se dřív jmenovala jako DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Přispěvatel Redis Cache](#redis-cache-contributor) | Umožňuje správu mezipamětí Redis, ale ne přístup k nim. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Přispěvatel databáze SQL](#sql-db-contributor) | Umožňuje spravovat databáze SQL, ale ne přístup k nim. Nemůžete také spravovat zásady související se zabezpečením nebo jejich nadřazené servery SQL. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Přispěvatel spravované instance SQL](#sql-managed-instance-contributor) | Umožňuje spravovat spravované instance SQL a požadovanou konfiguraci sítě, ale nemůže udělit přístup jiným uživatelům. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Správce zabezpečení SQL](#sql-security-manager) | Umožňuje spravovat zásady týkající se zabezpečení serverů a databází SQL, ale ne přístup k nim. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Přispěvatel SQL Server](#sql-server-contributor) | Umožňuje spravovat servery a databáze SQL, ale ne přístup k nim, a ne jejich zásady související se zabezpečením. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analýzy** |  |  |
> | [Vlastník dat Event Hubs Azure](#azure-event-hubs-data-owner) | Umožňuje úplný přístup k prostředkům Azure Event Hubs. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Přijímač dat Event Hubs Azure](#azure-event-hubs-data-receiver) | Umožňuje získat přístup k prostředkům Azure Event Hubs. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Odesilatel dat Event Hubs Azure](#azure-event-hubs-data-sender) | Povoluje odesílání přístup k prostředkům Azure Event Hubs. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Přispěvatel Data Factory](#data-factory-contributor) | Vytváření a Správa datových továren a také podřízených prostředků v nich. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Nástroj pro vyprázdnění dat](#data-purger) | Může vyprázdnit analytické údaje | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Operátor clusteru HDInsight](#hdinsight-cluster-operator) | Umožňuje číst a upravovat konfigurace clusteru HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Přispěvatel doménových služeb HDInsight](#hdinsight-domain-services-contributor) | Může číst, vytvářet, upravovat a odstraňovat služby související s doménami, které jsou potřeba pro HDInsight Balíček zabezpečení podniku | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Přispěvatel Log Analytics](#log-analytics-contributor) | Přispěvatel Log Analytics může číst všechna data monitorování a upravovat nastavení monitorování. Úprava nastavení monitorování zahrnuje přidání rozšíření virtuálního počítače do virtuálních počítačů. čtení klíčů účtu úložiště, aby bylo možné konfigurovat shromažďování protokolů z Azure Storage; vytváření a konfigurace účtů služby Automation; přidávání řešení; a konfigurují se diagnostiky Azure na všech prostředcích Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Čtenář Log Analytics](#log-analytics-reader) | Log Analytics čtenář může zobrazit a vyhledat všechna data monitorování a také zobrazit nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Přispěvatel registru schématu (Preview)](#schema-registry-contributor-preview) | Čtení, zápis a odstraňování skupin registru schémat a schémat. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Nástroj pro čtení registru schématu (Preview)](#schema-registry-reader-preview) | Čtení a výpis skupin registru a schémat registru. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Blockchain** |  |  |
> | [Přístup ke členskému uzlu blockchain (Preview)](#blockchain-member-node-access-preview) | Umožňuje přístup k blockchain členským uzlům. | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI a strojové učení** |  |  |
> | [Přispěvatel Cognitive Services](#cognitive-services-contributor) | Umožňuje vytvářet, číst, aktualizovat, odstraňovat a spravovat klíče Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Čtečka dat Cognitive Services (Preview)](#cognitive-services-data-reader-preview) | Umožňuje číst Cognitive Services data. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services uživatel](#cognitive-services-user) | Umožňuje číst a zobrazit seznam klíčů Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Mixed realita** |  |  |
> | [Správce vzdáleného vykreslování](#remote-rendering-administrator) | Poskytuje uživatele s možností převodu, správy relace, vykreslování a diagnostiky pro vzdálené vykreslování Azure. | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Klient vzdáleného vykreslování](#remote-rendering-client) | Poskytuje uživatele s možností spravovat relaci, vykreslování a diagnostiku pro vzdálené vykreslování Azure. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Přispěvatel účtu prostorových kotev](#spatial-anchors-account-contributor) | Umožňuje spravovat prostorové kotvy ve vašem účtu, ale neodstraňovat je. | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Vlastník účtu prostorových ukotvení](#spatial-anchors-account-owner) | Umožňuje spravovat prostorové kotvy v účtu, včetně jejich odstranění. | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Čtečka účtu prostorových kotev](#spatial-anchors-account-reader) | Umožňuje vyhledat a číst vlastnosti prostorových ukotvení ve vašem účtu. | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integrace** |  |  |
> | [Přispěvatel API Management služby](#api-management-service-contributor) | Může spravovat službu a rozhraní API. | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Role operátora služby API Management](#api-management-service-operator-role) | Může spravovat službu, ale ne rozhraní API. | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Role čtecího modulu služby API Management](#api-management-service-reader-role) | Přístup ke službě a rozhraním API jen pro čtení | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Vlastník dat konfigurace aplikace](#app-configuration-data-owner) | Umožňuje úplný přístup k datům konfigurace aplikace. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Čtečka dat konfigurace aplikace](#app-configuration-data-reader) | Povoluje přístup pro čtení k datům konfigurace aplikace. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus vlastník dat](#azure-service-bus-data-owner) | Umožňuje úplný přístup k Azure Service Bus prostředkům. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus přijímač dat](#azure-service-bus-data-receiver) | Umožňuje získat přístup k prostředkům Azure Service Bus. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus odesílatel dat](#azure-service-bus-data-sender) | Umožňuje odeslat přístup k prostředkům Azure Service Bus. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Vlastník registrace Azure Stack](#azure-stack-registration-owner) | Umožňuje spravovat Azure Stack registrace. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription Přispěvatel](#eventgrid-eventsubscription-contributor) | Umožňuje spravovat operace odběru událostí EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Čtecí modul EventSubscription EventGrid](#eventgrid-eventsubscription-reader) | Umožňuje číst odběry událostí EventGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Přispěvatel dat FHIR](#fhir-data-contributor) | Role umožňuje, aby měl uživatel nebo hlavní přístup k datům FHIR oprávnění. | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR data Exportér](#fhir-data-exporter) | Role umožňuje uživateli nebo objektu zabezpečení číst a exportovat FHIR data. | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [Čtecí modul dat FHIR](#fhir-data-reader) | Role umožňuje uživateli nebo objektu zabezpečení číst data FHIR. | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [Zapisovač dat FHIR](#fhir-data-writer) | Role umožňuje uživateli nebo objektu zabezpečení číst data FHIR a zapisovat je. | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Přispěvatel prostředí integrační služby](#integration-service-environment-contributor) | Umožňuje spravovat prostředí integračních služeb, ale ne přístup k nim. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [Vývojář prostředí integrační služby](#integration-service-environment-developer) | Umožňuje vývojářům vytvářet a aktualizovat pracovní postupy, účty pro integraci a připojení rozhraní API v prostředích integračních služeb. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Přispěvatel účtů inteligentních systémů](#intelligent-systems-account-contributor) | Umožňuje správu účtů inteligentních systémů, ale ne přístup k nim. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Přispěvatel aplikace logiky](#logic-app-contributor) | Umožňuje spravovat Logic Apps, ale ne měnit přístup k nim. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operátor aplikace logiky](#logic-app-operator) | Umožňuje číst, povolit a zakázat Logic Apps, ale nemůže je upravovat ani aktualizovat. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identita** |  |  |
> | [Přispěvatel spravovaných identit](#managed-identity-contributor) | Vytvoření, čtení, aktualizace a odstranění identity přiřazené uživatelem | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Spravovaný operátor identity](#managed-identity-operator) | Čtení a přiřazení identity přiřazené uživateli | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Zabezpečení** |  |  |
> | [Azure Sentinel Contributor](#azure-sentinel-contributor) (přispěvatel) | Azure Sentinel Contributor (přispěvatel) | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel Reader](#azure-sentinel-reader) (čtenář) | Azure Sentinel Reader (čtenář) | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel Responder](#azure-sentinel-responder) (reagující) | Azure Sentinel Responder | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Správce Key Vault (Preview)](#key-vault-administrator-preview) | Proveďte všechny operace roviny dat u trezoru klíčů a všech objektů, včetně certifikátů, klíčů a tajných klíčů. Nejde spravovat prostředky trezoru klíčů ani spravovat přiřazení rolí. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault úřední certifikát (Preview)](#key-vault-certificates-officer-preview) | S výjimkou oprávnění spravovat je možné provést jakoukoli akci s certifikáty trezoru klíčů. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Přispěvatel Key Vault](#key-vault-contributor) | Spravujte trezory klíčů, ale neumožňuje přiřadit role v Azure RBAC a neumožňuje přístup k tajným klíčům, klíčům nebo certifikátům. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault kryptografický pracovník (Preview)](#key-vault-crypto-officer-preview) | Proveďte jakoukoli akci s klíči trezoru klíčů s výjimkou oprávnění ke správě. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault šifrování šifrovací služby (Preview)](#key-vault-crypto-service-encryption-preview) | Čtení metadat klíčů a provádění operací zalamování nebo rozbalení. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault kryptografický uživatel (Preview)](#key-vault-crypto-user-preview) | Provádějte kryptografické operace pomocí klíčů. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault Reader (Preview)](#key-vault-reader-preview) | Číst metadata trezorů klíčů a jeho certifikátů, klíčů a tajných klíčů. Nelze číst citlivé hodnoty, jako je například obsah tajného klíče nebo klíčové materiály. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Úředník Key Vault tajných klíčů (Preview)](#key-vault-secrets-officer-preview) | Vykoná jakoukoli akci s tajnými kódy trezoru klíčů s výjimkou oprávnění ke správě. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Uživatel Key Vault tajných kódů (Preview)](#key-vault-secrets-user-preview) | Číst tajný obsah. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Správce zabezpečení](#security-admin) | Zobrazit a aktualizovat oprávnění pro Security Center. Stejná oprávnění jako role čtecího modulu zabezpečení a mohou také aktualizovat zásady zabezpečení a zapustit výstrahy a doporučení. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Přispěvatel vyhodnocení zabezpečení](#security-assessment-contributor) | Umožňuje doručovat hodnocení do Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Správce zabezpečení (starší verze)](#security-manager-legacy) | Toto je starší role. Místo toho prosím použijte Správce zabezpečení. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Čtenář zabezpečení](#security-reader) | Oprávnění k zobrazení Security Center. Může zobrazit doporučení, výstrahy, zásady zabezpečení a stavy zabezpečení, ale nemůže provádět změny. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Uživatel DevTest Labs](#devtest-labs-user) | Umožňuje připojit, spustit, restartovat a vypnout virtuální počítače ve vašem Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Autor testovacího prostředí](#lab-creator) | Umožňuje vytvořit novou laboratoř v rámci účtů Azure Lab. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitorování** |  |  |
> | [Přispěvatel Application Insights komponent](#application-insights-component-contributor) | Může spravovat součásti Application Insights. | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Poskytuje uživateli oprávnění k zobrazení a stažení snímků ladění shromážděných pomocí Snapshot Debugger Application Insights. Všimněte si, že tato oprávnění nejsou zahrnutá v rolích [vlastník](#owner) nebo [Přispěvatel](#contributor) . Když uživatelům udělíte roli Application Insights Snapshot Debugger, musíte roli udělit přímo uživateli. Role se při přidání do vlastní role nerozpoznala. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Přispěvatel monitorování](#monitoring-contributor) | Může číst všechna data monitorování a upravovat nastavení monitorování. Přečtěte si také téma [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Monitorování vydavatele metrik](#monitoring-metrics-publisher) | Povoluje publikování metrik pro prostředky Azure. | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Čtečka monitorování](#monitoring-reader) | Může číst všechna data monitorování (metriky, protokoly atd.). Přečtěte si také téma [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Přispěvatel sešitu](#workbook-contributor) | Může ukládat Sdílené sešity. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Čtenář sešitu](#workbook-reader) | Může číst sešity. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Správa a zásady správného řízení** |  |  |
> | [Operátor úlohy služby Automation](#automation-job-operator) | Vytvářejte a spravujte úlohy pomocí runbooků Automation. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operátor automatizace](#automation-operator) | Operátory automatizace můžou spouštět, zastavovat, pozastavovat a obnovovat úlohy. | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Operátor Runbooku služby Automation](#automation-runbook-operator) | Číst vlastnosti Runbooku – abyste mohli vytvářet úlohy Runbooku. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Připojování počítačů k Azure](#azure-connected-machine-onboarding) | Může připojit počítače připojené k Azure. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Správce prostředků počítačů připojených k Azure](#azure-connected-machine-resource-administrator) | Může číst, zapisovat, odstraňovat a znovu připojit počítače připojené k Azure. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Čtenář fakturace](#billing-reader) | Povolí přístup pro čtení k fakturačním údajům. | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Přispěvatel podrobného plánu](#blueprint-contributor) | Může spravovat definice podrobného plánu, ale nepřiřazovat je. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Operátor podrobného plánu](#blueprint-operator) | Může přiřadit existující publikované modrotisky, ale nemůže vytvářet nové plány. Všimněte si, že to funguje jenom v případě, že se přiřazení provádí pomocí uživatelem přiřazené spravované identity. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Přispěvatel Cost Management](#cost-management-contributor) | Umožňuje zobrazit náklady a spravovat konfiguraci nákladů (např. rozpočty, exporty). | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Čtecí modul Cost Management](#cost-management-reader) | Může zobrazit data a konfiguraci nákladů (např. rozpočty, exporty). | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Správce nastavení hierarchie](#hierarchy-settings-administrator) | Umožňuje uživatelům upravovat a odstraňovat nastavení hierarchie. | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Cluster Kubernetes – registrace k Azure ARC](#kubernetes-cluster---azure-arc-onboarding) | Definice role pro autorizaci libovolného uživatele nebo služby k vytvoření prostředku connectedClusters | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Role Přispěvatel spravovaných aplikací](#managed-application-contributor-role) | Umožňuje vytváření prostředků spravovaných aplikací. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Role operátora spravované aplikace](#managed-application-operator-role) | Umožňuje číst a provádět akce s prostředky spravovaných aplikací. | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Čtecí modul spravovaných aplikací](#managed-applications-reader) | Umožňuje číst prostředky ve spravované aplikaci a vyžadovat přístup JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Přiřazení registrace spravovaných služeb – odstranit roli](#managed-services-registration-assignment-delete-role) | Přiřazení registrace spravovaných služeb – odstranění role umožňuje správcům klienta odstranit přiřazení registrace přiřazené ke svému tenantovi. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Přispěvatel skupiny pro správu](#management-group-contributor) | Role Přispěvatel skupiny pro správu | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Čtenář skupiny pro správu](#management-group-reader) | Role čtenář skupiny pro správu | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Přispěvatel nového účtu Relic APM](#new-relic-apm-account-contributor) | Umožňuje správu účtů a aplikací New Relic Application Performance Management, ale ne přístup k nim. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Zapisovač dat zásad pro zásady (Preview)](#policy-insights-data-writer-preview) | Povolí přístup pro čtení k zásadám prostředků a k zápisu do událostí zásad součástí prostředků. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Přispěvatel zásad prostředků](#resource-policy-contributor) | Uživatelé s právy k vytváření a úpravám zásad prostředků, vytváření lístků podpory a čtení prostředků/hierarchie. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Přispěvatel Site Recovery](#site-recovery-contributor) | Umožňuje správu Site Recovery služby s výjimkou vytvoření trezoru a přiřazení role. | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operátor Site Recovery](#site-recovery-operator) | Umožňuje převzetí služeb při selhání a navrácení služeb po obnovení, ale jiné operace správy Site Recovery. | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Čtenář Site Recovery](#site-recovery-reader) | Umožňuje zobrazit Site Recovery stav, ale nemůže provádět jiné operace správy. | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Přispěvatel žádostí o podporu](#support-request-contributor) | Umožňuje vytvářet a spravovat žádosti o podporu. | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Přispěvatel značek](#tag-contributor) | Umožňuje spravovat značky entit bez poskytnutí přístupu k samotným entitám. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Další** |  |  |
> | [Vlastník dat digitálních vláken Azure](#azure-digital-twins-data-owner) | Role úplného přístupu pro data digitálních vláken s daty – rovina | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Čtečka dat digitálních vláken Azure](#azure-digital-twins-data-reader) | Role jen pro čtení pro digitální vlákna – vlastnosti roviny dat | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [Přispěvatel BizTalk](#biztalk-contributor) | Umožňuje spravovat BizTalk Services, ale ne přístup k nim. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Uživatel virtualizace plochy](#desktop-virtualization-user) | Umožňuje uživateli používat aplikace ve skupině aplikací. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Přispěvatel kolekcí úloh Scheduleru](#scheduler-job-collections-contributor) | Umožňuje spravovat kolekce úloh Scheduleru, ale ne přístup k nim. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Obecné


### <a name="contributor"></a>Přispěvatel

Udělí úplný přístup ke správě všech prostředků, ale neumožňuje přiřadit role v Azure RBAC. [Další informace](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | * | Vytváření a Správa prostředků všech typů |
> | **NotActions** |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/DELETE | Odstranění rolí, přiřazení zásad, definic zásad a sad zásad |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Vytváření rolí, přiřazení rolí, přiřazování zásad, definic zásad a definic sad zásad |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | Udělit volajícímu přístup Správce uživatelských přístupů v oboru tenanta |
> | [Microsoft. detail](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/Write | Vytvořit nebo aktualizovat jakékoli přiřazení podrobného plánu |
> | [Microsoft. detail](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/DELETE | Odstranit všechna přiřazení podrobného plánu |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Vlastník

Udělí úplný přístup ke správě všech prostředků, včetně možnosti přiřadit role v Azure RBAC. [Další informace](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | * | Vytváření a Správa prostředků všech typů |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Čtenář

Zobrazit všechny prostředky, ale neumožňuje provádět žádné změny. [Další informace](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Správce uživatelských přístupů

Umožňuje spravovat přístup uživatelů k prostředkům Azure. [Další informace](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/* | Spravovat autorizaci |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Výpočetní prostředky


### <a name="classic-virtual-machine-contributor"></a>Přispěvatel klasických virtuálních počítačů

Umožňuje správu klasických virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým se připojuje.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Vytváření a správa názvů klasických výpočetních domén |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Vytváření a správu virtuálních počítačů |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/JOIN/Action |  |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/Link/Action | Propojení rezervované IP adresy |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/Read | Načte rezervované IP adresy. |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/JOIN/Action | Připojí se k virtuální síti. |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/Read | Získat virtuální síť. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/Read | Vrátí disk účtu úložiště. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/Read | Vrátí image účtu úložiště. Zastaralé. Použijte Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Vypíše přístupové klíče pro účty úložiště. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Vrátí účet úložiště s daným účtem. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Přihlášení správce virtuálního počítače

Zobrazit Virtual Machines na portálu a přihlásit se jako správce [Další informace](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Získá definici veřejné IP adresy. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Získat definici virtuální sítě |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Získá definici nástroje pro vyrovnávání zatížení. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Načte definici síťového rozhraní.  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/Login/Action | Přihlaste se k virtuálnímu počítači jako běžný uživatel. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/Action | Přihlášení k virtuálnímu počítači pomocí oprávnění správce Windows nebo kořenového uživatele platformy Linux |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Přispěvatel virtuálních počítačů

Umožňuje správu virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým se připojuje. [Další informace](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Vytváření a Správa skupin dostupnosti výpočtů |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Locations/* | Vytváření a Správa výpočetních míst |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Proveďte všechny akce virtuálních počítačů, včetně možností vytváření, aktualizace, odstranění, spuštění, restartování a vypnutí virtuálních počítačů. Spustí předdefinované skripty na virtuálních počítačích. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Vytváření a správu škálovacích sad virtuálních počítačů |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/Write | Vytvoří nový disk nebo aktualizuje stávající. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/Read | Získá vlastnosti disku. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/DELETE | Odstraní disk. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Schedules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/JOIN/Action | Připojí se ke fondu back-end adres služby Application Gateway. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/JOIN/Action | Připojí fond adres back-endu nástroje pro vyrovnávání zatížení. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/JOIN/Action | Připojí příchozí fond NAT pro vyrovnávání zatížení. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/JOIN/Action | Připojí příchozí pravidlo NAT pro vyrovnávání zatížení. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/JOIN/Action | Umožňuje používat sondy nástroje pro vyrovnávání zatížení. Například s touto vlastností oprávnění healthProbe sady VM scaleing můžete odkazovat na test. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Získá definici nástroje pro vyrovnávání zatížení. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Locations/* | Vytváření a Správa síťových umístění |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Vytváření a Správa síťových rozhraní |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/JOIN/Action | Připojí se ke skupině zabezpečení sítě. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/Read | Získá definici skupiny zabezpečení sítě. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/JOIN/Action | Připojí se k veřejné IP adrese. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Získá definici veřejné IP adresy. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Získat definici virtuální sítě |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/JOIN/Action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Vytvořit záložní záměr ochrany |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Vrátí podrobnosti objektu chráněné položky. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Vytvořit položku chráněnou zálohováním |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Vrátí všechny zásady ochrany. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Write | Vytvoří zásady ochrany. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | Operace vytvořit trezor vytvoří prostředek Azure typu trezor. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Přihlášení uživatele virtuálního počítače

Zobrazte Virtual Machines na portálu a přihlaste se jako běžný uživatel. [Další informace](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Získá definici veřejné IP adresy. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Získat definici virtuální sítě |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Získá definici nástroje pro vyrovnávání zatížení. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Načte definici síťového rozhraní.  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/Login/Action | Přihlaste se k virtuálnímu počítači jako běžný uživatel. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Sítě


### <a name="cdn-endpoint-contributor"></a>Přispěvatel koncového bodu CDN

Může spravovat koncové body CDN, ale nemůže udělit přístup jiným uživatelům.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/Endpoints/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>Čtečka koncového bodu CDN

Může zobrazovat koncové body CDN, ale nemůže provádět změny.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/Endpoints/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Přispěvatel profilu CDN

Může spravovat profily CDN a jejich koncové body, ale nemůže udělit přístup jiným uživatelům. [Další informace](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>Čtečka profilů CDN

Může zobrazit profily CDN a jejich koncové body, ale nemůže provádět změny.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Přispěvatel klasických sítí

Umožňuje správu klasických sítí, ale ne přístup k nim. [Další informace](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Vytváření a Správa klasických sítí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>Přispěvatel zóny DNS

Umožňuje spravovat zóny a sady záznamů DNS v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup. [Další informace](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | Vytváření a Správa záznamů a zón DNS |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Přispěvatel sítě

Umožňuje spravovat sítě, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/* | Vytváření a správa sítí |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>Přispěvatel Privátní DNS zóny

Umožňuje spravovat soukromé prostředky zóny DNS, ale ne virtuální sítě, ke kterým jsou připojené. [Další informace](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Získat definici virtuální sítě |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/JOIN/Action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Přispěvatel Traffic Manager

Umožňuje správu profilů Traffic Manager, ale neumožňuje řídit, kdo k nim má přístup.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Storage


### <a name="avere-contributor"></a>Přispěvatel avere

Může vytvořit a spravovat cluster avere vFXT. [Další informace](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/*/Read |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/*/Read |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Získat definici virtuální sítě |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/Read | Získá definici podsítě virtuální sítě. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/JOIN/Action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Propojuje prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/JOIN/Action | Připojí se ke skupině zabezpečení sítě. Nelze upozorňovat. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/*/Read |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Vytvoření a správa účtů úložiště |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Resources/Read | Získá prostředky pro skupinu prostředků. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/DELETE | Vrátí výsledek odstranění objektu BLOB. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/Read | Vrátí objekt BLOB nebo seznam objektů BLOB. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/Write | Vrátí výsledek zápisu objektu BLOB. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere – operátor

, Které cluster avere vFXT používá ke správě clusteru, další [informace](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/Read | Získá vlastnosti virtuálního počítače. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Načte definici síťového rozhraní.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Write | Vytvoří síťové rozhraní nebo aktualizuje stávající síťové rozhraní.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Získat definici virtuální sítě |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/Read | Získá definici podsítě virtuální sítě. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/JOIN/Action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/JOIN/Action | Připojí se ke skupině zabezpečení sítě. Nelze upozorňovat. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/DELETE | Vrátí výsledek odstranění kontejneru. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Read | Vrátí seznam kontejnerů. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Write | Vrátí výsledek Put kontejneru objektů BLOB. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/DELETE | Vrátí výsledek odstranění objektu BLOB. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/Read | Vrátí objekt BLOB nebo seznam objektů BLOB. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/Write | Vrátí výsledek zápisu objektu BLOB. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Přispěvatel zálohování

Umožňuje spravovat službu zálohování, ale nemůže vytvářet trezory a [udělovat ostatním uživatelům](../backup/backup-rbac-rs-vault.md) přístup.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Získat definici virtuální sítě |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Správa výsledků operace při správě zálohování |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Vytváření a Správa zálohovacích kontejnerů v rámci záložních prostředků infrastruktury Recovery Services trezoru |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Aktualizuje seznam kontejnerů. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Vytváření a správa úloh zálohování |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Exportovat úlohy |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Vytváření a správa výsledků operací správy zálohování |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Vytváření a Správa zásad zálohování |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Vytváření a Správa položek, které se dají zálohovat |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Vytváření a Správa zálohovaných položek |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Vytváření a Správa kontejnerů držících zálohované položky |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Vrátí souhrny pro chráněné položky a chráněné servery pro Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/* | Vytváření a Správa certifikátů souvisejících se zálohováním v Recovery Services trezoru |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Vytváření a Správa rozšířených informací souvisejících s trezorem |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Získá výstrahy pro trezor služby Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Vytváření a Správa registrovaných identit |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Vytvoření a správa využití trezoru Recovery Services |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | Ověřit operaci pro chráněnou položku |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | Operace vytvořit trezor vytvoří prostředek Azure typu trezor. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Vrátí stav operace zálohování pro trezor Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Vrátí všechny servery pro správu zálohování zaregistrované s trezorem. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/Read | Získat všechny chráněné kontejnery |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Zkontroluje stav zálohování pro trezory Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupPreValidateProtection/Action |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Ověřit funkce |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Vyřeší upozornění. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Operace vrátí seznam operací pro poskytovatele prostředků. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/Read | Získá stav operace pro danou operaci. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Vypsat všechny záložní záměry ochrany |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Operátor záloh

Umožňuje správu zálohovacích služeb s výjimkou odebrání zálohování, vytváření trezoru a poskytování [přístupu jiným uživatelům](../backup/backup-rbac-rs-vault.md) .

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Získat definici virtuální sítě |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/Read | Vrátí stav operace. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/Read | Načte výsledek operace provedené na kontejneru ochrany. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Backup/Action | Provede zálohování chráněné položky. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Načte výsledek operace provedené na chráněných položkách. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Vrátí stav operace provedené na chráněných položkách. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Vrátí podrobnosti objektu chráněné položky. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Zřídit rychlé obnovení položky pro chráněnou položku |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Získat body obnovení pro chráněné položky. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | Obnoví body obnovení pro chráněné položky. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Odvolání okamžitého obnovení položky pro chráněnou položku |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Vytvořit položku chráněnou zálohováním |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Read | Vrátí všechny registrované kontejnery. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Aktualizuje seznam kontejnerů. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Vytváření a správa úloh zálohování |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Exportovat úlohy |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Vytváření a správa výsledků operací správy zálohování |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/Read | Načte výsledky operace zásad. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Vrátí všechny zásady ochrany. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Vytváření a Správa položek, které se dají zálohovat |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/Read | Vrátí seznam všech chráněných položek. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/Read | Vrátí všechny kontejnery patřící k předplatnému. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Vrátí souhrny pro chráněné položky a chráněné servery pro Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů k prostředku nebo úložišti. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Write | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Získá výstrahy pro trezor služby Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Operaci získat výsledky operace Get lze použít k získání stavu operace a výsledku asynchronně odeslané operace. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Operaci získat kontejnery lze použít k získání kontejnerů zaregistrovaných pro určitý prostředek. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Write | Operaci kontejneru registrovat službu lze použít k registraci kontejneru pomocí služby Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | Ověřit operaci pro chráněnou položku |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Vrátí stav operace zálohování pro trezor Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/Read | Získá stav operace zásad. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Write | Vytvoří registrovaný kontejner. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Inquire/Action | Dotazování na úlohy v rámci kontejneru |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Vrátí všechny servery pro správu zálohování zaregistrované s trezorem. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Vytvořit záložní záměr ochrany |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Read | Získat záložní záměr ochrany |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/Read | Získat všechny chráněné kontejnery |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/Read | Získat všechny položky v kontejneru |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Zkontroluje stav zálohování pro trezory Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupPreValidateProtection/Action |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Ověřit funkce |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Vyřeší upozornění. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Operace vrátí seznam operací pro poskytovatele prostředků. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/Read | Získá stav operace pro danou operaci. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Vypsat všechny záložní záměry ochrany |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Čtečka zálohování

Může zobrazovat služby zálohování, ale nemůže provádět změny. [Další informace](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | GetAllocatedStamp je interní operace, kterou používá služba. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/Read | Vrátí stav operace. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/Read | Načte výsledek operace provedené na kontejneru ochrany. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Načte výsledek operace provedené na chráněných položkách. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Vrátí stav operace provedené na chráněných položkách. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Vrátí podrobnosti objektu chráněné položky. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Získat body obnovení pro chráněné položky. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Read | Vrátí všechny registrované kontejnery. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/Read | Vrátí výsledek operace úlohy. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/Read | Vrátí všechny objekty úlohy. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Exportovat úlohy |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/Read | Vrátí výsledek operace zálohování pro Recovery Services trezor. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/Read | Načte výsledky operace zásad. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Vrátí všechny zásady ochrany. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/Read | Vrátí seznam všech chráněných položek. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/Read | Vrátí všechny kontejnery patřící k předplatnému. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Vrátí souhrny pro chráněné položky a chráněné servery pro Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Získá výstrahy pro trezor služby Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Operaci získat výsledky operace Get lze použít k získání stavu operace a výsledku asynchronně odeslané operace. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Operaci získat kontejnery lze použít k získání kontejnerů zaregistrovaných pro určitý prostředek. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/Read | Vrátí konfiguraci úložiště pro Recovery Services trezor. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/Read | Vrátí konfiguraci pro trezor Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Vrátí stav operace zálohování pro trezor Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/Read | Získá stav operace zásad. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Vrátí všechny servery pro správu zálohování zaregistrované s trezorem. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Read | Získat záložní záměr ochrany |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/Read | Získat všechny položky v kontejneru |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Zkontroluje stav zálohování pro trezory Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Vyřeší upozornění. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Operace vrátí seznam operací pro poskytovatele prostředků. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/Read | Získá stav operace pro danou operaci. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Vypsat všechny záložní záměry ochrany |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Ověřit funkce |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Přispěvatel klasických účtů úložiště

Umožňuje správu klasických účtů úložiště, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Vytvoření a správa účtů úložiště |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Role služby operátora klíče klasického účtu úložiště

Operátoři klíčů pro klasický účet úložiště můžou zobrazit a znovu vygenerovat klíče v klasických účtech úložiště. další [informace](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/Action | Vypíše přístupové klíče pro účty úložiště. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/RegenerateKey/Action | Obnoví existující přístupové klíče pro účet úložiště. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Přispěvatel Data Box

Umožňuje spravovat všechno, co služba Data Box, s výjimkou poskytnutí přístupu jiným uživatelům. [Další informace](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Čtecí modul Data Box

Umožňuje správu Data Box služby s výjimkou vytváření pořadí nebo úprav podrobností objednávky a udělení přístupu jiným uživatelům. [Další informace](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/*/Read |  |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Jobs/listsecrets/Action |  |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Jobs/listcredentials/Action | Zobrazí nešifrované přihlašovací údaje související s objednávkou. |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Locations/availableSkus/Action | Tato metoda vrátí seznam dostupných skladových položek. |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Locations/validateInputs/Action | Tato metoda provádí všechny typy ověření. |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Locations/regionConfiguration/Action | Tato metoda vrátí konfigurace pro oblast. |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Locations/validateAddress/Action | Ověří dodací adresu a v případě potřeby poskytne alternativní adresy. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Vývojář Data Lake Analytics

Umožňuje odesílat, monitorovat a spravovat vlastní úlohy, ale neumožňuje vytvářet ani odstraňovat Data Lake Analytics účty. [Další informace](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. BigAnalytics/účty/* |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | Microsoft. BigAnalytics/Accounts/DELETE |  |
> | Microsoft. BigAnalytics/Accounts/TakeOwnership/Action |  |
> | Microsoft. BigAnalytics/Accounts/Write |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/DELETE | Odstraňte účet DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/TakeOwnership/Action | Udělte oprávnění ke zrušení úloh odeslaných jinými uživateli. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/Write | Vytvořte nebo aktualizujte účet DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/dataLakeStoreAccounts/Write | Vytvoří nebo aktualizuje propojený účet DataLakeStore účtu DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/dataLakeStoreAccounts/DELETE | Odpojte účet DataLakeStore z účtu DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/storageAccounts/Write | Vytvoří nebo aktualizuje propojený účet úložiště účtu DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/storageAccounts/DELETE | Odpojte účet úložiště od účtu DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/firewallRules/Write | Vytvořte nebo aktualizujte pravidlo brány firewall. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/firewallRules/DELETE | Odstraní pravidlo brány firewall. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/computePolicies/Write | Vytvořte nebo aktualizujte výpočetní zásadu. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/computePolicies/DELETE | Odstraňte výpočetní zásadu. |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Čtenář a přístup k datům

Umožňuje zobrazit vše, ale neumožní vám odstranit ani vytvořit účet úložiště nebo obsažený prostředek. Umožní taky přístup pro čtení a zápis ke všem datům, která jsou obsažená v účtu úložiště, prostřednictvím přístupu k klíčům účtu úložiště.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/Action | Vrátí token SAS účtu pro zadaný účet úložiště. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Přispěvatel účtů úložiště

Umožňuje správu účtů úložiště. Poskytuje přístup k klíči účtu, který se dá použít pro přístup k datům přes autorizaci pomocí sdíleného klíče. [Další informace](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Vytvoří, aktualizuje nebo přečte nastavení diagnostiky pro Analysis Server |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Propojuje prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozorňovat. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Vytvoření a správa účtů úložiště |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Role služby operátora klíče účtu úložiště

Povoluje výpis a opětovné generování přístupových klíčů účtu úložiště. [Další informace](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/RegenerateKey/Action | Znovu vygeneruje přístupové klíče pro zadaný účet úložiště. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Přispěvatel dat v objektech blob služby Storage

Čtení, zápis a odstraňování kontejnerů Azure Storage a objektů BLOB. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Další informace](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/DELETE | Odstraní kontejner. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Read | Vrátí kontejner nebo seznam kontejnerů. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Write | Úprava metadat nebo vlastností kontejneru. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Vrátí klíč delegování uživatele pro Blob service. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/DELETE | Odstraní objekt BLOB. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/Read | Vrátí objekt BLOB nebo seznam objektů BLOB. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/Move/Action | Přesune objekt BLOB z jedné cesty na jiný. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/Write | Zápis do objektu BLOB. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Vlastník dat v objektech blob služby Storage

Poskytuje úplný přístup k Azure Storage kontejnerů a dat objektů blob, včetně přiřazování řízení přístupu k POSIX. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Další informace](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/* | Úplná oprávnění pro kontejnery |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Vrátí klíč delegování uživatele pro Blob service. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/* | Úplná oprávnění pro objekty blob |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Čtenář dat v objektech blob služby Storage

Čtení a výpis Azure Storage kontejnerů a objektů BLOB. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Další informace](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Read | Vrátí kontejner nebo seznam kontejnerů. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Vrátí klíč delegování uživatele pro Blob service. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/BLOBs/Read | Vrátí objekt BLOB nebo seznam objektů BLOB. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Delegování objektu BLOB úložiště

Získejte klíč pro delegování uživatelů, který se pak dá použít k vytvoření sdíleného přístupového podpisu pro kontejner nebo objekt blob, který je podepsaný pomocí přihlašovacích údajů Azure AD. Další informace najdete v tématu [Vytvoření SAS pro delegování uživatelů](/rest/api/storageservices/create-user-delegation-sas). [Další informace](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Vrátí klíč delegování uživatele pro Blob service. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Přispěvatel sdílené složky SMB dat souboru úložiště

Umožňuje čtení, zápis a odstraňování přístupu k souborům nebo adresářům ve sdílených složkách Azure. Tato role nemá na souborových serverech se systémem Windows žádný předdefinovaný ekvivalent. [Další informace](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Read | Vrátí soubor nebo složku nebo seznam souborů nebo složek. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Write | Vrátí výsledek zápisu souboru nebo vytvoření složky. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/DELETE | Vrátí výsledek odstranění souboru nebo složky. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Přispěvatel sdílené složky SMB dat souboru úložiště s vyššími oprávněními

Umožňuje číst, zapisovat, odstraňovat a upravovat seznamy ACL pro soubory nebo adresáře ve sdílených složkách Azure. Tato role je ekvivalentem seznamu ACL sdílené složky pro změnu na souborových serverech systému Windows. [Další informace](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Read | Vrátí soubor nebo složku nebo seznam souborů nebo složek. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Write | Vrátí výsledek zápisu souboru nebo vytvoření složky. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/DELETE | Vrátí výsledek odstranění souboru nebo složky. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/modifypermissions/Action | Vrátí výsledek úpravy oprávnění pro soubor nebo složku. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Čtenář sdílené složky SMB dat souboru úložiště

Umožňuje přístup pro čtení souborů nebo adresářů ve sdílených složkách Azure. Tato role je ekvivalentem seznamu ACL pro sdílení souborů na souborových serverech systému Windows. [Další informace](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Read | Vrátí soubor nebo složku nebo seznam souborů nebo složek. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Přispěvatel dat fronty úložiště

Čtení, zápis a odstraňování front Azure Storage a zpráv fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Další informace](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/DELETE | Odstraní frontu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Read | Vrátí frontu nebo seznam front. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Write | Úprava metadat nebo vlastností fronty |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Messages/DELETE | Odstraní jednu nebo více zpráv z fronty. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Messages/Read | Prohlížet nebo načíst jednu nebo více zpráv z fronty. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Messages/Write | Přidá zprávu do fronty. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Procesor zpráv s daty ve frontě úložiště

Prohlížet, načítat a odstraňovat zprávy z Azure Storage fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Další informace](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Messages/Read | Prohlížet zprávu |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Messages/Process/Action | Načte a odstraní zprávu. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Odesílatel zprávy s daty ve frontě úložiště

Přidejte zprávy do fronty Azure Storage. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Další informace](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Messages/Add/Action | Přidá zprávu do fronty. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Čtečka dat fronty úložiště

Čtení a výpis Azure Storage front a zpráv fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Další informace](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Read | Vrátí frontu nebo seznam front. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Messages/Read | Prohlížet nebo načíst jednu nebo více zpráv z fronty. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-reader"></a>Čtečka dat Azure Maps

Udělí přístup ke čtení dat souvisejících s mapou z účtu Azure Maps.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/Accounts/*/Read |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Přispěvatel Search Service

Umožňuje spravovat služby vyhledávání, ale ne přístup k nim. [Další informace](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Vytváření a správa vyhledávacích služeb |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-accesskey-reader"></a>Čtečka AccessKey signálu

Čtení klíčů služby signalizace

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/Read |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/Action | Zobrazení hodnoty přístupových klíčů signalizace na portálu pro správu nebo prostřednictvím rozhraní API |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>Server aplikace signaler (Preview)

Umožňuje službě signalizace přístupového serveru aplikace pomocí možností ověřování AAD.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/AccessKey/Action | Vygenerujte dočasnou AccessKey pro podepisování ClientTokens. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/Write | Spusťte připojení k serveru. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>Přispěvatel signálu

Vytváření, čtení, aktualizace a odstraňování prostředků služby signalizace

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>Přispěvatel bez serveru v Signal (Preview)

Umožňuje službě App Access v režimu bez serveru s možnostmi ověřování AAD.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/Action | Vygenerujte ClientToken pro spuštění připojení klienta. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>Vlastník služby signaler (Preview)

Úplný přístup k rozhraním REST API služby Azure Signal

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/Send/Action | Vysílá zprávy do všech připojení klientů v centru. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Send/Action | Zpráva všesměrového vysílání do skupiny |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Kontrolovat existenci skupiny nebo uživatelskou existenci ve skupině. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Write | Připojit nebo opustit skupinu. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Send/Action | Odesílá zprávy přímo do připojení klienta. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Read | Ověřte existenci připojení klienta. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Write | Ukončete připojení klienta. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Send/Action | Odeslat zprávy uživateli, který se může skládat z několika připojení klientů. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Ověřte existenci uživatele. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Write |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>Čtečka služby signaler (Preview)

Přístup jen pro čtení k rozhraním REST API služby Azure Signal

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Kontrolovat existenci skupiny nebo uživatelskou existenci ve skupině. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Read | Ověřte existenci připojení klienta. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Ověřte existenci uživatele. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Přispěvatel webového plánu

Umožňuje spravovat webové plány pro weby, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Vytváření a Správa serverových farem |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/JOIN/Action | Připojí se k App Service Environment. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Přispěvatel webu

Umožňuje spravovat weby (nikoli webové plány), ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Vytváření a Správa komponent Insights |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Certificates/* | Vytváření a Správa certifikátů webu |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/Read | Získá názvy webů přiřazených k názvu hostitele. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/JOIN/Action | Připojí App Service plán. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Získat vlastnosti v plánu App Service |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Sites/* | Vytváření a Správa webů (vytváření webů vyžaduje taky oprávnění k zápisu do přidruženého plánu App Service) |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Kontejnery


### <a name="acrdelete"></a>AcrDelete

ACR odstranit další [informace](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/DELETE | Odstraňte artefakt v registru kontejnerů. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

ACR image Signer [Další informace](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Sign/Write | Metadata vztahu důvěryhodnosti nabízených nebo vyžádaného obsahu pro registr kontejneru. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

[Další informace o](../container-registry/container-registry-roles.md) ACR pull

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Pull/Read | Vyžádání nebo získání imagí z registru kontejneru. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

[Další informace o](../container-registry/container-registry-roles.md) ACR

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Pull/Read | Vyžádání nebo získání imagí z registru kontejneru. |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/Write | Vložení nebo zápis imagí do registru kontejneru. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

čtečka dat karantény ACR

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/Read | Vyžádání nebo získání imagí v karanténě z registru kontejnerů |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

zapisovač dat karantény ACR

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/Read | Vyžádání nebo získání imagí v karanténě z registru kontejnerů |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/Write | Zápis/Změna stavu karantény imagí v karanténě |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Role Správce clusteru služby Azure Kubernetes

Vypíše akci přihlašovacích údajů správce clusteru. [Další informace](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/Action | Výpis přihlašovacích údajů clusterAdmin spravovaného clusteru |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/Action | Získání profilu přístupu spravovaného clusteru podle názvu role pomocí přihlašovacích údajů pro seznam |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Získání spravovaného clusteru |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Role uživatele clusteru služby Azure Kubernetes

Vypíše akci přihlašovacích údajů uživatele clusteru. [Další informace](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Výpis přihlašovacích údajů clusterUser spravovaného clusteru |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Získání spravovaného clusteru |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Role přispěvatele služby Azure Kubernetes

Udělí přístup ke čtení a zápisu clusterů služby Azure Kubernetes. [Další informace](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Získání spravovaného clusteru |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Write | Vytvoří nový spravovaný cluster nebo aktualizuje stávající. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Správce RBAC služby Azure Kubernetes

Umožňuje spravovat všechny prostředky v rámci clusteru nebo oboru názvů, s výjimkou kvót prostředků a obory názvů pro aktualizaci nebo odstranění. [Další informace](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Vytvoří nebo aktualizuje nasazení. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Získá seznam předplatných. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Výpis přihlašovacích údajů clusterUser spravovaného clusteru |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Write | Zapisuje resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/DELETE | Odstraní resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Write | Zapisuje obory názvů |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/DELETE | Odstraní obory názvů. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Správce clusteru RBAC služby Azure Kubernetes

Umožňuje správu všech prostředků v clusteru. [Další informace](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Vytvoří nebo aktualizuje nasazení. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Získá seznam předplatných. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Výpis přihlašovacích údajů clusterUser spravovaného clusteru |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Čtečka RBAC služby Azure Kubernetes

Povoluje přístup jen pro čtení k zobrazení většiny objektů v oboru názvů. Nepovoluje zobrazování rolí nebo vazeb rolí. Tato role nepovoluje zobrazení tajných kódů, protože čtení obsahu tajných kódů umožňuje přístup k přihlašovacím údajům ServiceAccount v oboru názvů, což by mohlo povolit přístup k rozhraní API jako libovolný ServiceAccount v oboru názvů (forma eskalace oprávnění). Použití této role v oboru clusteru umožní přístup napříč všemi obory názvů. [Další informace](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Vytvoří nebo aktualizuje nasazení. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Získá seznam předplatných. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/controllerrevisions/Read | Přečte controllerrevisions |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/daemonsets/Read | Přečte daemonsets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/Deployments/Read | Načte nasazení |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/replicasets/Read | Přečte replicasets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/statefulsets/Read | Přečte statefulsets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/AutoScaling/horizontalpodautoscalers/Read | Přečte horizontalpodautoscalers |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Batch/cronjobs/Read | Přečte cronjobs |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Batch/Jobs/Read | Čte úlohy |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/Read | Přečte configmaps |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Endpoints/Read | Přečte koncové body. |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.k8s.IO/Events/Read | Čte události |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/Read | Čte události |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/Read | Přečte daemonsets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/Read | Načte nasazení |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/Read | Načte příchozí přenosy. |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/Read | Přečte networkpolicies |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/replicasets/Read | Přečte replicasets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/Read | Přečte limitranges |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Read | Čtení oborů názvů |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/ingresses/Read | Načte příchozí přenosy. |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/networkpolicies/Read | Přečte networkpolicies |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/Read | Přečte persistentvolumeclaims |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/Read | Čtení lusků |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/Read | Přečte poddisruptionbudgets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/Read | Přečte replicationcontrollers |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/Read | Přečte replicationcontrollers |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Read | Přečte resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/Read | Přečte serviceaccounts |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/Read | Čte služby |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Zapisovač RBAC služby Azure Kubernetes

Povoluje přístup pro čtení a zápis většiny objektů v oboru názvů. Tato role nepovoluje zobrazování ani úpravy rolí nebo vazeb rolí. Tato role však umožňuje přístup k tajným klíčům a spuštění lusků jako libovolných ServiceAccount v oboru názvů, takže se dá použít k získání úrovní přístupu rozhraní API všech ServiceAccount v oboru názvů. Použití této role v oboru clusteru umožní přístup napříč všemi obory názvů. [Další informace](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Vytvoří nebo aktualizuje nasazení. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Získá seznam předplatných. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/controllerrevisions/Read | Přečte controllerrevisions |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/daemonsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/Deployments/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/replicasets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/statefulsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/AutoScaling/horizontalpodautoscalers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Batch/cronjobs/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Batch/Jobs/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Endpoints/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.k8s.IO/Events/Read | Čte události |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/Read | Čte události |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/replicasets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/Read | Přečte limitranges |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Read | Čtení oborů názvů |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/ingresses/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/networkpolicies/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Read | Přečte resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Secrets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Databáze


### <a name="cosmos-db-account-reader-role"></a>Role čtečky účtu Cosmos DB

Může číst data Azure Cosmos DB účtu. Správa účtů Azure Cosmos DB najdete v tématu [Přispěvatel účtu DocumentDB](#documentdb-account-contributor) . [Další informace](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/Read | Čtení libovolné kolekce |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/Action | Přečte klíče databázového účtu jen pro čtení. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Čtení definic metriky |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Čtení metrik |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Operátor Cosmos DB

Umožňuje správu účtů Azure Cosmos DB, ale ne přístup k datům v nich. Zabraňuje přístup k klíčům účtu a připojovacím řetězcům. [Další informace](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Propojuje prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozorňovat. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Může odeslat žádost o obnovení pro databázi Cosmos DB nebo kontejner pro účet. další [informace](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/Backup/Action | Odeslat žádost o konfiguraci zálohování |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/Restore/Action | Odeslat žádost o obnovení |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Přispěvatel účtu DocumentDB

Může spravovat účty Azure Cosmos DB. Azure Cosmos DB se dřív jmenovala jako DocumentDB. [Další informace](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Vytváření a Správa účtů Azure Cosmos DB |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Propojuje prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozorňovat. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Přispěvatel Redis Cache

Umožňuje správu mezipamětí Redis, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. cache](resource-provider-operations.md#microsoftcache)/Register/Action | Zaregistruje poskytovatele prostředků Microsoft. cache v rámci předplatného. |
> | [Microsoft. cache](resource-provider-operations.md#microsoftcache)/Redis/* | Vytváření a Správa mezipamětí Redis |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Přispěvatel databáze SQL

Umožňuje spravovat databáze SQL, ale ne přístup k nim. Nemůžete také spravovat zásady související se zabezpečením nebo jejich nadřazené servery SQL. [Další informace](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/* | Vytvoření a správa databází SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Čtení metrik |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Čtení definic metriky |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditingSettings/* | Upravit nastavení auditu |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditRecords/Read | Načtení záznamů auditu objektu BLOB databáze |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/dataMaskingPolicies/* | Upravit zásady maskování dat |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityAlertPolicies/* | Upravit zásady výstrah zabezpečení |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityMetrics/* | Upravit metriky zabezpečení |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>Přispěvatel spravované instance SQL

Umožňuje spravovat spravované instance SQL a požadovanou konfiguraci sítě, ale nemůže udělit přístup jiným uživatelům.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/instanceFailoverGroups/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Čtení metrik |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Čtení definic metriky |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/DELETE | Odstraní konkrétní spravovaný server Azure Active Directory jenom objekt ověřování. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/Write | Přidá nebo aktualizuje určitý spravovaný server Azure Active Directory jenom objekt ověřování. |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>Správce zabezpečení SQL

Umožňuje spravovat zásady týkající se zabezpečení serverů a databází SQL, ale ne přístup k nim. [Další informace](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Propojuje prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozorňovat. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/administratorAzureAsyncOperation/Read | Načte výsledek operací správce asynchronního správce Azure Managed instance. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingSettings/* | Vytvoření a Správa nastavení auditování SQL serveru |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/extendedAuditingSettings/Read | Načíst podrobnosti o zásadách auditování rozšířených objektů BLOB serveru nakonfigurovaných na daném serveru |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditingSettings/* | Vytvoření a Správa nastavení auditování databáze systému SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditRecords/Read | Načtení záznamů auditu objektu BLOB databáze |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/dataMaskingPolicies/* | Vytváření a Správa zásad pro maskování dat databáze SQL serveru |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/extendedAuditingSettings/Read | Načte podrobnosti o rozšířených zásadách auditování objektů BLOB konfigurovaných pro danou databázi. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/Read | Vrátí seznam databází nebo získá vlastnosti zadané databáze. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Read | Získá schéma databáze. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Columns/Read | Získá sloupec databáze. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Read | Získat databázovou tabulku. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityAlertPolicies/* | Vytváření a Správa zásad výstrah zabezpečení databáze serveru SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityMetrics/* | Vytváření a Správa metrik zabezpečení databáze systému SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/firewallRules/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityAlertPolicies/* | Vytváření a Správa zásad výstrah zabezpečení systému SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Read | Vrátí seznam spravovaných instancí nebo získá vlastnosti zadané spravované instance. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Přispěvatel SQL Server

Umožňuje spravovat servery a databáze SQL, ale ne přístup k nim, a ne jejich zásady související se zabezpečením. [Další informace](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/* | Vytváření a Správa serverů SQL |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Čtení metrik |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Čtení definic metriky |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingSettings/* | Upravit nastavení auditování SQL serveru |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditingSettings/* | Upravit nastavení auditování databáze systému SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditRecords/Read | Načtení záznamů auditu objektu BLOB databáze |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/dataMaskingPolicies/* | Upravit zásady maskování dat databáze systému SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityAlertPolicies/* | Upravit zásady výstrah zabezpečení databáze systému SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityMetrics/* | Upravit metriky zabezpečení databáze systému SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityAlertPolicies/* | Upravit zásady výstrah zabezpečení systému SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/DELETE | Odstraní určitý server Azure Active Directory jenom objekt ověřování. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/Write | Přidá nebo aktualizuje určitý server Azure Active Directory jenom objekt ověřování. |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analýzy


### <a name="azure-event-hubs-data-owner"></a>Vlastník dat Event Hubs Azure

Umožňuje úplný přístup k prostředkům Azure Event Hubs. [Další informace](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Přijímač dat Event Hubs Azure

Umožňuje získat přístup k prostředkům Azure Event Hubs. [Další informace](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Odesilatel dat Event Hubs Azure

Povoluje odesílání přístup k prostředkům Azure Event Hubs. [Další informace](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/Read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Send/Action |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Přispěvatel Data Factory

Vytváření a Správa datových továren a také podřízených prostředků v nich. [Další informace](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Vytváření a Správa datových továrn a podřízených prostředků v nich. |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/Factories/* | Vytváření a Správa datových továrn a podřízených prostředků v nich. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Write | Vytvořit nebo aktualizovat eventSubscription |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Nástroj pro vyprázdnění dat

Může vyprázdnit analytické údaje. [Další informace](../azure-monitor/platform/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/Purge/Action | Vymazání dat z Application Insights |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Zobrazení dat Log Analytics |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Purge/Action | Odstranit zadaná data z pracovního prostoru |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>Operátor clusteru HDInsight

Umožňuje číst a upravovat konfigurace clusteru HDInsight. [Další informace](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/*/Read |  |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/getGatewaySettings/Action | Získat nastavení brány pro cluster HDInsight |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/updateGatewaySettings/Action | Aktualizace nastavení brány pro cluster HDInsight |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/Configurations/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Načte nebo vypíše operace nasazení. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Přispěvatel doménových služeb HDInsight

Může číst, vytvářet, upravovat a odstraňovat související operace služby Domain Services, které jsou potřeba pro HDInsight Balíček zabezpečení podniku další [informace](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md) .

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. aad](resource-provider-operations.md#microsoftaad)/*/Read |  |
> | [Microsoft. aad](resource-provider-operations.md#microsoftaad)/domainServices/*/Read |  |
> | [Microsoft. aad](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Přispěvatel Log Analytics

Přispěvatel Log Analytics může číst všechna data monitorování a upravovat nastavení monitorování. Úprava nastavení monitorování zahrnuje přidání rozšíření virtuálního počítače do virtuálních počítačů. čtení klíčů účtu úložiště, aby bylo možné konfigurovat shromažďování protokolů z Azure Storage; vytváření a konfigurace účtů služby Automation; přidávání řešení; a konfigurují se diagnostiky Azure na všech prostředcích Azure. [Další informace](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Vypíše přístupové klíče pro účty úložiště. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Extensions/Write | Nainstaluje nebo aktualizuje rozšíření ARC Azure. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Vytvoří, aktualizuje nebo přečte nastavení diagnostiky pro Analysis Server |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Deployments/* |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Čtenář Log Analytics

Log Analytics čtenář může zobrazit a vyhledat všechna data monitorování a také zobrazit nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. [Další informace](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Hledání pomocí nového stroje |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Spustí vyhledávací dotaz. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Read | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>Přispěvatel registru schématu (Preview)

Čtení, zápis a odstraňování skupin registru schémat a schémat.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Nástroj pro čtení registru schématu (Preview)

Čtení a výpis skupin registru a schémat registru.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemagroups/Read | Získá seznam popisů prostředků ve schématu. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemas/Read | Načtení schémat |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Přístup ke členskému uzlu blockchain (Preview)

Umožňuje přístup k uzlům členů blockchain. [Další informace](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Read | Získá nebo zobrazí seznam existujících uzlů transakcí členů blockchain. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Connect/Action | Připojí se k uzlu transakce člena blockchain. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI a strojové učení


### <a name="cognitive-services-contributor"></a>Přispěvatel Cognitive Services

Umožňuje vytvářet, číst, aktualizovat, odstraňovat a spravovat klíče Cognitive Services. [Další informace](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Cognitiveservices Account](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft. Features](resource-provider-operations.md#microsoftfeatures)/Features/Read | Získá funkce předplatného. |
> | [Microsoft. Features](resource-provider-operations.md#microsoftfeatures)/Providers/Features/Read | Získá funkci předplatného v daném poskytovateli prostředků. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Vytvoří, aktualizuje nebo přečte nastavení diagnostiky pro Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/Read | Čtení definic protokolů |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/Read | Čtení definic metriky |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Čtení metrik |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Načte nebo vypíše operace nasazení. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Získá seznam předplatných. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Deployments/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Čtečka dat Cognitive Services (Preview)

Umožňuje číst Cognitive Services data.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. cognitiveservices Account](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services uživatel

Umožňuje číst a zobrazit seznam klíčů Cognitive Services. [Další informace](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. cognitiveservices Account](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Microsoft. cognitiveservices Account](resource-provider-operations.md#microsoftcognitiveservices)/Accounts/listkeys/Action | Zobrazit seznam klíčů |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Čtení klasické výstrahy metriky |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/Read | Přečíst nastavení diagnostiky prostředků |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/Read | Čtení definic protokolů |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/Read | Čtení definic metriky |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Čtení metrik |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Načte nebo vypíše operace nasazení. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Získá seznam předplatných. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Cognitiveservices Account](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Smíšená realita


### <a name="remote-rendering-administrator"></a>Správce vzdáleného vykreslování

Poskytuje uživatele s možností převodu, správy relace, vykreslování a diagnostiky pro vzdálené vykreslování Azure. další [informace](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Action | Spustit převod assetu |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Read | Získat vlastnosti převodu assetu |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/DELETE | Zastavit převod assetu |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Získat vlastnosti relace |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Spustit relace |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/DELETE | Zastavit relace |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Render/Read | Připojení k relaci |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Připojit k inspektoru vzdáleného vykreslování |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Klient vzdáleného vykreslování

Poskytuje uživatele s možností spravovat relaci, vykreslování a diagnostiku pro vzdálené vykreslování Azure. [Další informace](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Získat vlastnosti relace |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Spustit relace |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/DELETE | Zastavit relace |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Render/Read | Připojení k relaci |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Připojit k inspektoru vzdáleného vykreslování |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Přispěvatel účtu prostorových kotev

Umožňuje spravovat prostorové kotvy ve vašem účtu, ale neodstraňují je. další [informace](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Vytvořit prostorové kotvy |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Vyhledat nejbližší prostorové kotvy |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Získat vlastnosti prostorových ukotvení |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Vyhledat prostorové kotvy |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Odeslat diagnostická data, která vám pomůžou zlepšit kvalitu služby prostorových kotev Azure |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Aktualizovat vlastnosti prostorových ukotvení |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Vlastník účtu prostorových ukotvení

Umožňuje spravovat prostorové kotvy v účtu, včetně jejich odstranění. další [informace](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Vytvořit prostorové kotvy |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/DELETE | Odstranit prostorové kotvy |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Vyhledat nejbližší prostorové kotvy |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Získat vlastnosti prostorových ukotvení |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Vyhledat prostorové kotvy |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Odeslat diagnostická data, která vám pomůžou zlepšit kvalitu služby prostorových kotev Azure |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Aktualizovat vlastnosti prostorových ukotvení |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Čtečka účtu prostorových kotev

Umožňuje vyhledat a číst vlastnosti prostorových ukotvení ve vašem účtu. další [informace](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Vyhledat nejbližší prostorové kotvy |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Získat vlastnosti prostorových ukotvení |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Vyhledat prostorové kotvy |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Odeslat diagnostická data, která vám pomůžou zlepšit kvalitu služby prostorových kotev Azure |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integrace


### <a name="api-management-service-contributor"></a>Přispěvatel API Management služby

Může spravovat službu a rozhraní API. [Další informace](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/* | Vytvoření a Správa služby API Management |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Role operátora služby API Management

Může spravovat službu, ale ne rozhraní API, další [informace](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/*/Read | Číst API Management instance služby |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Backup/Action | Služba Backup API Management do zadaného kontejneru v účtu úložiště zadaného uživatelem |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/DELETE | Odstranit instanci služby API Management |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/managedeployments/Action | Změna SKU/jednotek, přidání nebo odebrání regionálních nasazení API Management služby |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Read | Čtení metadat pro instanci služby API Management Service |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Restore/Action | Obnovení služby API Management ze zadaného kontejneru v účtu úložiště zadaného uživatelem |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/updatecertificate/Action | Nahrání certifikátu TLS/SSL pro službu API Management |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/updatehostname/Action | Instalace, aktualizace nebo odebrání názvů vlastních domén pro službu API Management |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Write | Vytvořit nebo aktualizovat instanci služby API Management |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Users/Keys/Read | Získání klíčů přidružených k uživateli |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Role čtecího modulu služby API Management

Přístup ke službě a rozhraním API jen pro čtení [Další informace](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/*/Read | Číst API Management instance služby |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Read | Čtení metadat pro instanci služby API Management Service |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Users/Keys/Read | Získání klíčů přidružených k uživateli |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>Vlastník dat konfigurace aplikace

Umožňuje úplný přístup k datům konfigurace aplikace. [Další informace](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Write |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/DELETE |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Čtečka dat konfigurace aplikace

Povoluje přístup pro čtení k datům konfigurace aplikace. [Další informace](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus vlastník dat

Umožňuje úplný přístup k Azure Service Bus prostředkům. [Další informace](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus přijímač dat

Umožňuje získat přístup k prostředkům Azure Service Bus. [Další informace](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus odesílatel dat

Umožňuje odeslat přístup k prostředkům Azure Service Bus. [Další informace](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Send/Action |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Vlastník registrace Azure Stack

Umožňuje spravovat Azure Stack registrace.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/Read | Získání vlastností předplatného Azure Stack Edge |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/* za akci |  |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/Read | Získá vlastnosti produktu Azure Stack Marketplace. |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Read | Získá vlastnosti registrace Azure Stack. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription Přispěvatel

Umožňuje spravovat operace odběru událostí EventGrid. [Další informace](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Vypsat globální odběry událostí podle typu tématu |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/eventSubscriptions/Read | Výpis odběrů místních událostí |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/topicTypes/eventSubscriptions/Read | Výpis odběrů místních událostí podle TopicType |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>Čtecí modul EventSubscription EventGrid

Umožňuje číst odběry událostí EventGrid. [Další informace](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Read | Přečíst eventSubscription |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Vypsat globální odběry událostí podle typu tématu |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/eventSubscriptions/Read | Výpis odběrů místních událostí |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/topicTypes/eventSubscriptions/Read | Výpis odběrů místních událostí podle TopicType |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>Přispěvatel dat FHIR

Role umožňuje uživateli nebo hlavnímu základnímu přístupu k datům FHIR [získat další informace](../healthcare-apis/configure-azure-rbac.md) .

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHIR data Exportér

Role umožňuje uživateli nebo objektu zabezpečení číst a exportovat FHIR data. další [informace](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/Read | Čtení prostředků FHIR (zahrnuje hledání a historii verzí).  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/export/Action | Operace exportu ($export). |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>Čtecí modul dat FHIR

Role umožňuje uživateli nebo objektu zabezpečení číst data FHIR. další [informace](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/Read | Čtení prostředků FHIR (zahrnuje hledání a historii verzí).  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>Zapisovač dat FHIR

Role umožňuje uživateli nebo objektu zabezpečení číst a zapisovat FHIR data. další [informace](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/* |  |
> | **NotDataActions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/hardDelete/Action | Pevné odstranění (včetně historie verzí). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>Přispěvatel prostředí integrační služby

Umožňuje spravovat prostředí integračních služeb, ale ne přístup k nim. [Další informace](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>Vývojář prostředí integrační služby

Umožňuje vývojářům vytvářet a aktualizovat pracovní postupy, účty pro integraci a připojení rozhraní API v prostředích integračních služeb. [Další informace](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/Read | Přečte prostředí integrační služby. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/JOIN/Action | Spojí prostředí integrační služby. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Přispěvatel účtů inteligentních systémů

Umožňuje správu účtů inteligentních systémů, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | Microsoft. IntelligentSystems/účty/* | Vytváření a Správa účtů inteligentních systémů |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Přispěvatel aplikace logiky

Umožňuje spravovat Logic Apps, ale ne měnit přístup k nim. [Další informace](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Vypíše přístupové klíče pro účty úložiště. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Vrátí účet úložiště s daným účtem. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Vytvoří, aktualizuje nebo přečte nastavení diagnostiky pro Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. Vypíše kategorie protokolů v protokolu aktivit. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Čtení definic metriky (seznam dostupných typů metrik pro prostředek). |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/* | Spravuje prostředky Logic Apps. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Vytvoření a správa brány připojení. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/* | Vytvoření a Správa připojení. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/* | Vytvoří a spravuje vlastní rozhraní API. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/JOIN/Action | Připojí App Service plán. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Získat vlastnosti v plánu App Service |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/sites/Functions/listSecrets/Action | Výpis tajných kódů funkcí |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Operátor aplikace logiky

Umožňuje číst, povolit a zakázat Logic Apps, ale nemůže je upravovat ani aktualizovat. [Další informace](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/Read | Pravidla výstrah pro čtení přehledů |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/Read | Získá nastavení diagnostiky pro Logic Apps |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/Read | Získá dostupné metriky pro Logic Apps. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/*/Read | Přečte Logic Apps prostředky. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Disable/Action | Zakáže pracovní postup. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Enable/Action | Povolí pracovní postup. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Validate/Action | Ověří pracovní postup. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Načte nebo vypíše operace nasazení. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/Read | Čtení bran připojení. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/*/Read | Čtení připojení. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/*/Read | Číst vlastní rozhraní API. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Získat vlastnosti v plánu App Service |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identita


### <a name="managed-identity-contributor"></a>Přispěvatel spravovaných identit

Vytvoření, čtení, aktualizace a odstranění identity přiřazené uživatelem další [informace](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Read | Získá existující identitu přiřazenou uživatelem. |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Write | Vytvoří novou identitu přiřazenou uživatelem nebo aktualizuje značky přidružené k existující identitě přiřazené uživatelem. |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/DELETE | Odstraní existující identitu přiřazenou uživatelem. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Spravovaný operátor identity

Čtení a přiřazení identity přiřazené uživateli další [informace](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Read |  |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Assign/Action |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Zabezpečení


### <a name="azure-sentinel-contributor"></a>Azure Sentinel Contributor (přispěvatel)

Azure Sentinel Přispěvatel – další [informace](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Hledání pomocí nového stroje |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Zobrazení dat Log Analytics |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Získat ukončení řešení OMS |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Spouštění dotazů nad daty v pracovním prostoru |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Získat zdroje dat v pracovním prostoru. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel Reader (čtenář)

[Další informace o](../sentinel/roles.md) čtečce Azure Sentinel

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Ověřit autorizaci a licenci uživatele |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/Query/Action | Dotazování na indikátory analýzy hrozeb |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Dotazování na indikátory analýzy hrozeb |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Hledání pomocí nového stroje |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Zobrazení dat Log Analytics |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/LinkedServices/Read | Získejte propojené služby v daném pracovním prostoru. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Načte uložený vyhledávací dotaz. |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Získat ukončení řešení OMS |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Spouštění dotazů nad daty v pracovním prostoru |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Získat zdroje dat v pracovním prostoru. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Přečíst sešit |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel Responder

Azure Sentinel Responder – další [informace](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Ověřit autorizaci a licenci uživatele |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/Cases/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/Incidents/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/appendTags/Action | Připojit značky k indikátoru analýzy hrozeb |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/Query/Action | Dotazování na indikátory analýzy hrozeb |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/Action | Inteligentní značky – Analýza hrozeb |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/appendTags/Action | Připojit značky k indikátoru analýzy hrozeb |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/replaceTags/Action | Nahradit značky indikátoru analýzy hrozeb |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Dotazování na indikátory analýzy hrozeb |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Hledání pomocí nového stroje |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Zobrazení dat Log Analytics |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Získat zdroje dat v pracovním prostoru. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Načte uložený vyhledávací dotaz. |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Získat ukončení řešení OMS |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Spouštění dotazů nad daty v pracovním prostoru |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Získat zdroje dat v pracovním prostoru. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Přečíst sešit |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator-preview"></a>Správce Key Vault (Preview)

Proveďte všechny operace roviny dat u trezoru klíčů a všech objektů, včetně certifikátů, klíčů a tajných klíčů. Nejde spravovat prostředky trezoru klíčů ani spravovat přiřazení rolí. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | /CheckNameAvailability/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Kontroluje, jestli je název trezoru klíčů platný a nepoužívá se. |
> | /DeletedVaults/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazit vlastnosti provizorních odstraněných trezorů klíčů |
> | [Microsoft./Locations/trezor](resource-provider-operations.md#microsoftkeyvault)*/Read |  |
> | [Microsoft./Vaults/trezor](resource-provider-operations.md#microsoftkeyvault)*/Read |  |
> | /Operations/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazí seznam operací, které jsou k dispozici v poskytovateli prostředků Microsoft. klíčů. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Trezor Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer-preview"></a>Key Vault úřední certifikát (Preview)

S výjimkou oprávnění spravovat je možné provést jakoukoli akci s certifikáty trezoru klíčů. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | /CheckNameAvailability/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Kontroluje, jestli je název trezoru klíčů platný a nepoužívá se. |
> | /DeletedVaults/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazit vlastnosti provizorních odstraněných trezorů klíčů |
> | [Microsoft./Locations/trezor](resource-provider-operations.md#microsoftkeyvault)*/Read |  |
> | [Microsoft./Vaults/trezor](resource-provider-operations.md#microsoftkeyvault)*/Read |  |
> | /Operations/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazí seznam operací, které jsou k dispozici v poskytovateli prostředků Microsoft. klíčů. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Trezor Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/certificatecas/* |  |
> | [Trezor Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/Certificates/* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Přispěvatel Key Vault

Spravujte trezory klíčů, ale neumožňuje přiřadit role v Azure RBAC a neumožňuje přístup k tajným klíčům, klíčům nebo certifikátům. [Další informace](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Trezor Microsoft.](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | /Locations/deletedVaults/Purge/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Vymazání tichého odstraněného trezoru klíčů |
> | [Trezor Microsoft.](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Trezor Microsoft.](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer-preview"></a>Key Vault kryptografický pracovník (Preview)

Proveďte jakoukoli akci s klíči trezoru klíčů s výjimkou oprávnění ke správě. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | /CheckNameAvailability/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Kontroluje, jestli je název trezoru klíčů platný a nepoužívá se. |
> | /DeletedVaults/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazit vlastnosti provizorních odstraněných trezorů klíčů |
> | [Microsoft./Locations/trezor](resource-provider-operations.md#microsoftkeyvault)*/Read |  |
> | [Microsoft./Vaults/trezor](resource-provider-operations.md#microsoftkeyvault)*/Read |  |
> | /Operations/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazí seznam operací, které jsou k dispozici v poskytovateli prostředků Microsoft. klíčů. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Trezor Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-preview"></a>Key Vault šifrování šifrovací služby (Preview)

Čtení metadat klíčů a provádění operací zalamování nebo rozbalení. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | /Vaults/Keys/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazí seznam klíčů v zadaném trezoru nebo vlastnosti pro čtení a veřejný materiál klíče. U asymetrických klíčů Tato operace zveřejňuje veřejný klíč a zahrnuje schopnost provádět algoritmy veřejných klíčů, jako je například šifrování a ověření podpisu. Soukromé klíče a symetrické klíče nejsou nikdy zpřístupněny. |
> | /Vaults/Keys/Wrap/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zabalte symetrický klíč pomocí Key Vaultho klíče. Všimněte si, že pokud je klíč Key Vault asymetrický, tato operace se dá provést s přístupem pro čtení. |
> | /Vaults/Keys/Unwrap/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Rozbalení symetrického klíče pomocí Key Vaultho klíče |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user-preview"></a>Key Vault kryptografický uživatel (Preview)

Provádějte kryptografické operace pomocí klíčů. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | /Vaults/Keys/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazí seznam klíčů v zadaném trezoru nebo vlastnosti pro čtení a veřejný materiál klíče. U asymetrických klíčů Tato operace zveřejňuje veřejný klíč a zahrnuje schopnost provádět algoritmy veřejných klíčů, jako je například šifrování a ověření podpisu. Soukromé klíče a symetrické klíče nejsou nikdy zpřístupněny. |
> | /Vaults/Keys/Update/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Aktualizuje zadané atributy přidružené k danému klíči. |
> | /Vaults/Keys/Backup/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Vytvoří záložní soubor klíče. Soubor se může použít k obnovení klíče v Key Vault stejného předplatného. Můžou platit omezení. |
> | /Vaults/Keys/Encrypt/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Šifrujte prostý text klíčem. Všimněte si, že pokud je klíč asymetrické, tato operace může být provedena pomocí objektů zabezpečení s přístupem pro čtení. |
> | /Vaults/Keys/Decrypt/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Dešifrování šifrovaného textu pomocí klíče |
> | /Vaults/Keys/Wrap/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zabalte symetrický klíč pomocí Key Vaultho klíče. Všimněte si, že pokud je klíč Key Vault asymetrický, tato operace se dá provést s přístupem pro čtení. |
> | /Vaults/Keys/Unwrap/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Rozbalení symetrického klíče pomocí Key Vaultho klíče |
> | /Vaults/Keys/Sign/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Podepište hash klíčem. |
> | /Vaults/Keys/Verify/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Ověřte hodnotu hash. Všimněte si, že pokud je klíč asymetrické, tato operace může být provedena pomocí objektů zabezpečení s přístupem pro čtení. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader-preview"></a>Key Vault Reader (Preview)

Číst metadata trezorů klíčů a jeho certifikátů, klíčů a tajných klíčů. Nelze číst citlivé hodnoty, jako je například obsah tajného klíče nebo klíčové materiály. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | /CheckNameAvailability/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Kontroluje, jestli je název trezoru klíčů platný a nepoužívá se. |
> | /DeletedVaults/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazit vlastnosti provizorních odstraněných trezorů klíčů |
> | [Microsoft./Locations/trezor](resource-provider-operations.md#microsoftkeyvault)*/Read |  |
> | [Microsoft./Vaults/trezor](resource-provider-operations.md#microsoftkeyvault)*/Read |  |
> | /Operations/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazí seznam operací, které jsou k dispozici v poskytovateli prostředků Microsoft. klíčů. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft./Vaults/trezor](resource-provider-operations.md#microsoftkeyvault)*/Read |  |
> | /Vaults/Secrets/readMetadata/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Vypíše nebo zobrazí vlastnosti tajného klíče, ale ne jeho hodnotu. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer-preview"></a>Úředník Key Vault tajných klíčů (Preview)

Vykoná jakoukoli akci s tajnými kódy trezoru klíčů s výjimkou oprávnění ke správě. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | /CheckNameAvailability/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Kontroluje, jestli je název trezoru klíčů platný a nepoužívá se. |
> | /DeletedVaults/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazit vlastnosti provizorních odstraněných trezorů klíčů |
> | [Microsoft./Locations/trezor](resource-provider-operations.md#microsoftkeyvault)*/Read |  |
> | [Microsoft./Vaults/trezor](resource-provider-operations.md#microsoftkeyvault)*/Read |  |
> | /Operations/Read [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Zobrazí seznam operací, které jsou k dispozici v poskytovateli prostředků Microsoft. klíčů. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Trezor Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/Secrets/* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user-preview"></a>Uživatel Key Vault tajných kódů (Preview)

Číst tajný obsah. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | /Vaults/Secrets/getSecret/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Získá hodnotu tajného kódu. |
> | /Vaults/Secrets/readMetadata/Action [trezoru Microsoft.](resource-provider-operations.md#microsoftkeyvault) | Vypíše nebo zobrazí vlastnosti tajného klíče, ale ne jeho hodnotu. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Správce zabezpečení

Zobrazit a aktualizovat oprávnění pro Security Center. Stejná oprávnění jako role čtecího modulu zabezpečení a mohou také aktualizovat zásady zabezpečení a zapustit výstrahy a doporučení. [Další informace](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Vytvoření a správa přiřazení zásad |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Vytváření a Správa definic zásad |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Vytváření a Správa sad zásad |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Zobrazení dat Log Analytics |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Vytváření a Správa bezpečnostních komponent a zásad |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policyExemptions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Přispěvatel vyhodnocení zabezpečení

Umožňuje doručovat hodnocení do Security Center

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/Assessments/Write | Vytvořit nebo aktualizovat posouzení zabezpečení u předplatného |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Správce zabezpečení (starší verze)

Toto je starší role. Místo toho prosím použijte Správce zabezpečení.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/Read | Čtení informací o konfiguraci klasických virtuálních počítačů |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/Write | Zápis konfigurace pro klasické virtuální počítače |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/Read | Přečíst informace o konfiguraci klasické sítě |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Vytváření a Správa bezpečnostních komponent a zásad |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Čtenář zabezpečení

Oprávnění k zobrazení Security Center. Může zobrazit doporučení, výstrahy, zásady zabezpečení a stavy zabezpečení, ale nemůže provádět změny. [Další informace](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Čtení klasické výstrahy metriky |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Zobrazení dat Log Analytics |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/*/Read |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/*/Read | Číst součásti a zásady zabezpečení |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/*/Read |  |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>Uživatel DevTest Labs

Umožňuje připojit, spustit, restartovat a vypnout virtuální počítače ve vašem Azure DevTest Labs. [Další informace](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/availabilitySets/Read | Získá vlastnosti skupiny dostupnosti. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read | Přečtěte si vlastnosti virtuálního počítače (velikosti virtuálních počítačů, běhový stav, rozšíření virtuálních počítačů atd.). |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/Action | Vyžádá virtuální počítač a uvolní výpočetní prostředky. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/Read | Získá vlastnosti virtuálního počítače. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/Action | Restartuje virtuální počítač. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/Start/Action | Spustí virtuální počítač. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/Read | Čtení vlastností testovacího prostředí |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/claimAnyVm/Action | Vydeklarujte náhodný virtuální počítač v testovacím prostředí. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/createEnvironment/Action | Vytvářejte virtuální počítače v testovacím prostředí. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/ensureCurrentUserProfile/Action | Ujistěte se, že aktuální uživatel má v testovacím prostředí platný profil. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/DELETE | Odstraňte vzorce. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/Read | Čtení vzorců. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/Write | Přidat nebo upravit vzorce. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/policySets/evaluatePolicies/Action | Vyhodnotí zásady testovacího prostředí. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/Claim/Action | Převezme vlastnictví existujícího virtuálního počítače. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/VirtualMachines/listApplicableSchedules/Action | Zobrazí seznam použitelných plánů spuštění/zastavení, pokud existují. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/getRdpFileContents/Action | Načte řetězec, který představuje obsah souboru RDP pro virtuální počítač. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/JOIN/Action | Připojí fond adres back-endu nástroje pro vyrovnávání zatížení. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/JOIN/Action | Připojí příchozí pravidlo NAT pro vyrovnávání zatížení. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/Read | Čtení vlastností síťového rozhraní (například všech nástrojů pro vyrovnávání zatížení, které jsou součástí síťového rozhraní) |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/JOIN/Action | Připojí virtuální počítač k síťovému rozhraní. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Načte definici síťového rozhraní.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Write | Vytvoří síťové rozhraní nebo aktualizuje stávající síťové rozhraní.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/Read | Čtení vlastností veřejné IP adresy |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/JOIN/Action | Připojí se k veřejné IP adrese. Nelze upozorňovat. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Získá definici veřejné IP adresy. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/JOIN/Action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Načte nebo vypíše operace nasazení. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Načte nebo vypíše nasazení. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | **NotActions** |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/Read | Zobrazí seznam dostupných velikostí, na které se dá virtuální počítač aktualizovat. |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Autor testovacího prostředí

Umožňuje vytvořit novou laboratoř v rámci účtů Azure Lab. [Další informace](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/Read |  |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/Action | Vytvořte testovací prostředí v účtu testovacího prostředí. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/Action | Získejte ceny a dostupnost kombinací velikostí, geografických oblastí a operačních systémů pro účet testovacího prostředí. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/Action | Získat základní omezení a využití pro toto předplatné |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Monitorování


### <a name="application-insights-component-contributor"></a>Přispěvatel Application Insights komponent

Může spravovat součásti Application Insights [Další informace](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytváření a Správa pravidel pro klasických výstrah |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Vytváření a Správa nových pravidel upozornění |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Vytváření a Správa komponent Insights |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Vytváření a správa webových testů Insights |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger

Poskytuje uživateli oprávnění k zobrazení a stažení snímků ladění shromážděných pomocí Snapshot Debugger Application Insights. Všimněte si, že tato oprávnění nejsou zahrnutá v rolích [vlastník](#owner) nebo [Přispěvatel](#contributor) . Když uživatelům udělíte roli Application Insights Snapshot Debugger, musíte roli udělit přímo uživateli. Role se při přidání do vlastní role nerozpoznala. [Další informace](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Přispěvatel monitorování

Může číst všechna data monitorování a upravovat nastavení monitorování. Přečtěte si také téma [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). [Další informace](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Alerts/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Vytváření a Správa komponent Insights |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Vytvoří, aktualizuje nebo přečte nastavení diagnostiky pro Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/EventTypes/* | Vypíše události protokolu aktivit (události správy) v předplatném. Toto oprávnění platí pro programový i portálový přístup k protokolu aktivit. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. Vypíše kategorie protokolů v protokolu aktivit. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Čtení definic metriky (seznam dostupných typů metrik pro prostředek). |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Načte metriky pro prostředek. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registrace poskytovatele Microsoft Insights |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Vytváření a správa webových testů Insights |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Write | Vytvoří nový pracovní prostor nebo vytvoří odkaz na existující pracovní prostor tím, že mu poskytne ID zákazníka z existujícího pracovního prostoru. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/intelligencepacks/* | Balíčky řešení Log Analytics pro čtení, zápis a odstranění. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* | Čtení, zápis a odstraňování uložených hledání Log Analytics. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Spustí vyhledávací dotaz. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Action | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/storageinsightconfigs/* | Čtení, zápis a odstraňování konfigurací Log Analytics úložiště |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. monitor zátěže byl](resource-provider-operations.md#microsoftworkloadmonitor)/Monitors/* |  |
> | [Microsoft. monitor zátěže byl](resource-provider-operations.md#microsoftworkloadmonitor)/notificationSettings/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Monitorování vydavatele metrik

Umožňuje publikovat metriky pro prostředky Azure. další [informace](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registrace poskytovatele Microsoft Insights |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Zapsat metriky |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Čtečka monitorování

Může číst všechna data monitorování (metriky, protokoly atd.). Přečtěte si také téma [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). [Další informace](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Spustí vyhledávací dotaz. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Přispěvatel sešitu

Může ukládat Sdílené sešity. [Další informace](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Write | Vytvoří nebo aktualizuje sešit. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/DELETE | Odstranění sešitu |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Přečíst sešit |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Čtenář sešitu

Může číst sešity. [Další informace](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Přečíst sešit |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Správa a zásady správného řízení


### <a name="automation-job-operator"></a>Operátor úlohy služby Automation

Vytvářejte a spravujte úlohy pomocí runbooků Automation. [Další informace](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/Read | Přečte Hybrid Runbook Worker prostředky. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Read | Načte Azure Automation úlohu. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Resume/Action | Obnoví Azure Automation úlohu. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/stop/Action | Zastaví úlohu Azure Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Streams/Read | Získá datový proud úlohy Azure Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Suspend/Action | Pozastaví úlohu Azure Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Write | Vytvoří úlohu Azure Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Output/Read | Načte výstup úlohy. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Operátor služby Automation

Operátory automatizace můžou spouštět, zastavovat, pozastavovat a obnovovat úlohy. další [informace](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/Read | Přečte Hybrid Runbook Worker prostředky. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Read | Načte Azure Automation úlohu. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Resume/Action | Obnoví Azure Automation úlohu. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/stop/Action | Zastaví úlohu Azure Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Streams/Read | Získá datový proud úlohy Azure Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Suspend/Action | Pozastaví úlohu Azure Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Write | Vytvoří úlohu Azure Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/Read | Získá Azure Automation plán úlohy. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/Write | Vytvoří plán úlohy Azure Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/Read | Získá pracovní prostor propojený s účtem Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Read | Získá účet Azure Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/Read | Získá Azure Automation Runbook. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Schedules/Read | Získá prostředek plánování Azure Automation. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Schedules/Write | Vytvoří nebo aktualizuje prostředek plánování Azure Automation. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Output/Read | Načte výstup úlohy. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Operátor Runbooku služby Automation

Číst vlastnosti Runbooku – abyste mohli vytvářet úlohy Runbooku. [Další informace](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/Read | Získá Azure Automation Runbook. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Připojování počítačů k Azure

Může připojit počítače připojené k Azure. [Další informace](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Read | Čtení jakýchkoli počítačů s obloukem Azure ARC |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Write | Zapisuje počítače s obloukem Azure ARC. |
> | [Microsoft. GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/Read | Získat přiřazení konfigurace hostů. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Správce prostředků počítačů připojených k Azure

Může číst, zapisovat, odstraňovat a znovu připojit počítače připojené k Azure.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Read | Čtení jakýchkoli počítačů s obloukem Azure ARC |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Write | Zapisuje počítače s obloukem Azure ARC. |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/DELETE | Odstraní počítače s obloukem Azure ARC. |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/reconnect/Action |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Extensions/Write | Nainstaluje nebo aktualizuje rozšíření ARC Azure. |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/Read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Čtenář fakturace

Povolit přístup pro čtení k údajům o fakturaci [Další informace](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. fakturace](resource-provider-operations.md#microsoftbilling)/*/Read | Přečíst fakturační informace |
> | [Microsoft. Commerce](resource-provider-operations.md#microsoftcommerce)/*/Read |  |
> | [Microsoft. spotřeb](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Přispěvatel podrobného plánu

Může spravovat definice podrobného plánu, ale nepřiřazovat je. [Další informace](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. detail](resource-provider-operations.md#microsoftblueprint)/Blueprints/* | Vytvářejte a spravujte definice podrobného plánu nebo artefakty podrobného plánu. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Operátor podrobného plánu

Může přiřadit existující publikované modrotisky, ale nemůže vytvářet nové plány. Všimněte si, že to funguje jenom v případě, že se přiřazení provádí pomocí uživatelem přiřazené spravované identity. [Další informace](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. detail](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Vytvořte a spravujte přiřazení podrobného plánu. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Přispěvatel Cost Management

Umožňuje zobrazit náklady a spravovat konfiguraci nákladů (např. rozpočty, exporty) [Další informace](../cost-management-billing/costs/understand-work-scopes.md) .

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. spotřeba](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft./BillingPeriods/Read fakturace](resource-provider-operations.md#microsoftbilling) |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Získá seznam předplatných. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Configurations/Read | Získat konfigurace |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Recommendations/Read | Čte doporučení |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Čtecí modul Cost Management

Může zobrazit data a konfiguraci nákladů (např. rozpočty, exporty) [Další informace](../cost-management-billing/costs/understand-work-scopes.md) .

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. spotřeb](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Microsoft./BillingPeriods/Read fakturace](resource-provider-operations.md#microsoftbilling) |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Získá seznam předplatných. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Configurations/Read | Získat konfigurace |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Recommendations/Read | Čte doporučení |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>Správce nastavení hierarchie

Umožňuje uživatelům upravovat a odstraňovat nastavení hierarchie.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Settings/Write | Vytvoří nebo aktualizuje nastavení hierarchie skupiny pro správu. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Settings/DELETE | Odstraní nastavení hierarchie skupiny pro správu. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Cluster Kubernetes – registrace k Azure ARC

Definice role pro autorizaci libovolného uživatele nebo služby k vytvoření prostředku connectedClusters [Další informace](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Vytvoří nebo aktualizuje nasazení. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Získá seznam předplatných. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Zapisuje connectedClusters |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Read | Číst connectedClusters |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Role Přispěvatel spravovaných aplikací

Umožňuje vytváření prostředků spravovaných aplikací.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/* |  |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Register/Action | Zaregistrujte se do řešení. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Role operátora spravované aplikace

Umožňuje číst a provádět akce s prostředky spravovaných aplikací.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/Read | Načte seznam aplikací. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/* za akci |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Čtecí modul spravovaných aplikací

Umožňuje číst prostředky ve spravované aplikaci a vyžadovat přístup JIT.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Přiřazení registrace spravovaných služeb – odstranit roli

Přiřazení registrace spravovaných služeb – odstranění role umožňuje správcům klienta odstranit přiřazení registrace přiřazené ke svému tenantovi. [Další informace](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/Read | Načte seznam přiřazení registrace spravovaných služeb. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/DELETE | Odebere přiřazení registrace spravovaných služeb. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/Read | Přečte stav operace pro daný prostředek. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Přispěvatel skupiny pro správu

Role Přispěvatel skupiny pro správu – další [informace](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/DELETE | Odstraní skupinu pro správu. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/DELETE | Odpojí předplatné ze skupiny pro správu. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/Write | Přidruží stávající předplatné ke skupině pro správu. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Write | Vytvoří nebo aktualizuje skupinu pro správu. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/Read | Vypíše předplatné v rámci dané skupiny pro správu. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Čtenář skupiny pro správu

Role čtenář skupiny pro správu

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/Read | Vypíše předplatné v rámci dané skupiny pro správu. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Přispěvatel nového účtu Relic APM

Umožňuje správu účtů a aplikací New Relic Application Performance Management, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | NewRelic. APM/účty/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Zapisovač dat zásad pro zásady (Preview)

Povolí přístup pro čtení k zásadám prostředků a k zápisu do událostí zásad součástí prostředků. [Další informace](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/Read | Získejte informace o přiřazení zásady. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/Read | Získat informace o definici zásady. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/Read | Získat informace o výjimce zásady. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/Read | Získejte informace o definici sady zásad. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/Action | Zkontroluje stav dodržování předpisů dané součásti proti zásadám dat. |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/Action | Zaprotokoluje události zásad součásti prostředků. |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policyexemptions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Přispěvatel zásad prostředků

Uživatelé s právy k vytváření a úpravám zásad prostředků, vytváření lístků podpory a čtení prostředků/hierarchie. [Další informace](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Vytvoření a správa přiřazení zásad |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/* | Vytváření a Správa definic zásad |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Vytváření a Správa sad zásad |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policyexemptions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Přispěvatel Site Recovery

Umožňuje správu Site Recovery služby s výjimkou vytvoření trezoru a přiřazení role. [Další informace](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Získat definici virtuální sítě |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | GetAllocatedStamp je interní operace, kterou používá služba. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocateStamp/Action | AllocateStamp je interní operace, kterou používá služba. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů k prostředku nebo úložišti. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Vytváření a Správa rozšířených informací souvisejících s trezorem |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Vytváření a Správa registrovaných identit |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/* | Vytvořit nebo aktualizovat nastavení výstrah replikace |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Čtení jakýchkoli událostí |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/* | Vytváření a Správa prostředků infrastruktury replikace |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Vytváření a správa úloh replikace |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/* | Vytváření a Správa zásad replikace |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/* | Vytváření a Správa plánů obnovení |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Vytvoření a Správa konfigurace úložiště Recovery Servicesho trezoru |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | Operaci tokenu trezoru jde použít k získání tokenu trezoru pro operace back-endu na úrovni trezoru. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Čtení výstrah pro trezor služby Recovery Services |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationOperationStatus/Read | Přečtěte si stav operace replikace trezoru |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Operátor Site Recovery

Umožňuje převzetí služeb při selhání a navrácení služeb po obnovení, ale neprovádí jiné operace správy Site Recovery další [informace](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Získat definici virtuální sítě |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | GetAllocatedStamp je interní operace, kterou používá služba. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocateStamp/Action | AllocateStamp je interní operace, kterou používá služba. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Operaci získat výsledky operace Get lze použít k získání stavu operace a výsledku asynchronně odeslané operace. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Operaci získat kontejnery lze použít k získání kontejnerů zaregistrovaných pro určitý prostředek. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/Read | Přečtěte si všechna nastavení výstrah. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Čtení jakýchkoli událostí |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/checkConsistency/Action | Kontroluje konzistenci prostředků infrastruktury. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/Read | Čtení jakýchkoli prostředků infrastruktury |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/reassociateGateway/Action | Znovu přidružit bránu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/renewcertificate/Action | Prodloužit platnost certifikátu pro Fabric |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/Read | Čtení všech sítí |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Číst všechna mapování sítě |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/Read | Čtení všech kontejnerů ochrany |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Čtení všech chráněných položek |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/Action | Použít bod obnovení |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/Action | Potvrzení převzetí služeb při selhání |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/Action | Plánované převzetí služeb při selhání |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Čtení jakýchkoli chráněných položek |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Čtení všech bodů obnovení replikace |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/Action | Opravit replikaci |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/Action | Znovu chránit chráněnou položku |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Přepnout kontejner ochrany |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/Action | Testovací převzetí služeb při selhání |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/Action | Vyčištění testovacího převzetí služeb při selhání |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Převzetí služeb při selhání |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/Action | Aktualizovat službu mobility |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Přečíst všechna mapování kontejnerů ochrany |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Číst všechny poskytovatele Recovery Services |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Aktualizovat zprostředkovatele |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/Read | Čtení všech klasifikací úložiště |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Čtení jakýchkoli mapování klasifikací úložiště |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/Read | Číst libovolný servery vCenter |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Vytváření a správa úloh replikace |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/Read | Čtení všech zásad |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/failoverCommit/Action | Plán obnovení potvrzení převzetí služeb při selhání |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/plannedFailover/Action | Plán obnovení plánovaného převzetí služeb při selhání |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/Read | Čtení všech plánů obnovení |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/reProtect/Action | Znovu zapnout ochranu plánu obnovení |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailover/Action | Plán obnovení testovacího převzetí služeb při selhání |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailoverCleanup/Action | Plán obnovení pro vyčištění testovacího převzetí služeb při selhání |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/unplannedFailover/Action | Plán obnovení převzetí služeb při selhání |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Čtení výstrah pro trezor služby Recovery Services |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | Operaci tokenu trezoru jde použít k získání tokenu trezoru pro operace back-endu na úrovni trezoru. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Čtenář Site Recovery

Umožňuje zobrazit Site Recovery stav, ale nemůže provádět jiné operace správy. další [informace](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | GetAllocatedStamp je interní operace, kterou používá služba. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Získá výstrahy pro trezor služby Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Operaci získat výsledky operace Get lze použít k získání stavu operace a výsledku asynchronně odeslané operace. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Operaci získat kontejnery lze použít k získání kontejnerů zaregistrovaných pro určitý prostředek. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/Read | Přečtěte si všechna nastavení výstrah. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Čtení jakýchkoli událostí |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/Read | Čtení jakýchkoli prostředků infrastruktury |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/Read | Čtení všech sítí |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Číst všechna mapování sítě |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/Read | Čtení všech kontejnerů ochrany |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Čtení všech chráněných položek |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Čtení jakýchkoli chráněných položek |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Čtení všech bodů obnovení replikace |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Přečíst všechna mapování kontejnerů ochrany |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Číst všechny poskytovatele Recovery Services |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/Read | Čtení všech klasifikací úložiště |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Čtení jakýchkoli mapování klasifikací úložiště |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/Read | Číst libovolný servery vCenter |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/Read | Čtení všech úloh |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/Read | Čtení všech zásad |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/Read | Čtení všech plánů obnovení |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | Operaci tokenu trezoru jde použít k získání tokenu trezoru pro operace back-endu na úrovni trezoru. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Přispěvatel žádostí o podporu

Umožňuje vytvářet a spravovat žádosti o podporu. další [informace](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Přispěvatel značek

Umožňuje spravovat značky entit bez poskytnutí přístupu k samotným entitám. [Další informace](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Resources/Read | Získá prostředky pro skupinu prostředků. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Resources/Read | Získá prostředky předplatného. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Tags/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Ostatní


### <a name="azure-digital-twins-data-owner"></a>Vlastník dat digitálních vláken Azure

Role úplného přístupu pro data digitálních vláken – rovinní další [informace](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Čtení, odstranění, vytvoření nebo aktualizace jakékoli trasy událostí |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Čtení, vytváření, aktualizace nebo odstraňování jakýchkoli digitálních vláken |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Commands/* | Vyvolání libovolného příkazu u digitálního vlákna |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Relationships/* | Čtení, vytváření, aktualizace nebo odstraňování jakýchkoli digitálních zdvojených vztahů |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Models/* | Čtení, vytvoření, aktualizace nebo odstranění jakéhokoli modelu |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Query/* | Dotazování libovolného grafu digitálních vláken |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Čtečka dat digitálních vláken Azure

Role jen pro čtení pro data digitálního vlákna – vlastnosti roviny – další [informace](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Read | Čtení jakýchkoli digitálních vláken |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Relationships/Read | Čtení všech digitálních zdvojených vztahů |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/Read | Čtení libovolné trasy události |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Models/Read | Číst libovolný model |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Query/Action | Dotazování libovolného grafu digitálních vláken |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>Přispěvatel BizTalk

Umožňuje spravovat BizTalk Services, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. BizTalkServices/BizTalk/* | Vytváření a Správa BizTalk Services |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Uživatel virtualizace plochy

Umožňuje uživateli používat aplikace ve skupině aplikací. [Další informace](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | Microsoft. DesktopVirtualization/applicationGroups/useApplications/Action | Použití aplikace |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Přispěvatel kolekcí úloh Scheduleru

Umožňuje spravovat kolekce úloh Scheduleru, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Čtení rolí a přiřazení rolí |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Vytvoření a Správa klasického upozornění na metriku |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Vytvoření a Správa nasazení |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | [Microsoft. Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Vytváření a Správa kolekcí úloh |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Vytvoření a aktualizace lístku podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce dataactions** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Další kroky

- [Spárovat poskytovatele prostředků se službou](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Vlastní role Azure](custom-roles.md)
- [Oprávnění ve službě Azure Security Center](../security-center/security-center-permissions.md)
