---
title: Integrované role Azure pro Azure RBAC
description: Tento článek popisuje předdefinované role Azure pro řízení přístupu na základě rolí Azure (RBAC). Obsahuje seznam akcí, notactions, dataactions a NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 2fe3b94463da07304f2c853910ac5d2a6771d070
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545639"
---
# <a name="azure-built-in-roles"></a>Předdefinované role Azure

[Řízení přístupu na základě rolí Azure (RBAC)](overview.md) má několik předdefinovaných rolí Azure, které můžete přiřadit uživatelům, skupinám, instančním objektům a spravovaným identitám. Přiřazení rolí jsou způsob, jakým řídíte přístup k prostředkům Azure. Pokud předdefinované role nesplňují specifické potřeby vaší organizace, můžete si vytvořit vlastní [vlastní role Azure](custom-roles.md).

Tento článek uvádí předdefinované role pro prostředky Azure, které se neustále vyvíjejí. Chcete-li získat nejnovější role, použijte [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) nebo [az seznam definic role](/cli/azure/role/definition#az-role-definition-list). Pokud hledáte role správce pro Azure Active Directory (Azure AD), přečtěte si informace [o oprávněních rolí správce ve službě Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="all"></a>Všechny

Následující tabulka obsahuje stručný popis a jedinečné ID každé předdefinované role. Vyberte název role, chcete-li `DataActions`zobrazit `NotDataActions` seznam , `Actions` `NotActions`, a pro každou roli. Informace o tom, co tyto akce znamenají a jak se vztahují na správu a datové roviny, najdete [v tématu Principy definic rolí pro prostředky Azure](role-definitions.md).


> [!div class="mx-tableFixed"]
> | Vestavěná role | Popis | ID |
> | --- | --- | --- |
> | **Obecné** |  |  |
> | [Přispěvatel](#contributor) | Umožňuje spravovat vše kromě udělení přístupu k prostředkům. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Vlastník](#owner) | Umožňuje spravovat vše, včetně přístupu k prostředkům. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Čtenář](#reader) | Umožňuje zobrazit vše, ale neprovádět žádné změny. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Správce uživatelského přístupu](#user-access-administrator) | Umožňuje spravovat přístup uživatelů k prostředkům Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Compute** |  |  |
> | [Klasický přispěvatel virtuálního počítače](#classic-virtual-machine-contributor) | Umožňuje spravovat klasické virtuální počítače, ale ne přístup k nim, a ne virtuální síť nebo účet úložiště, ke kterým jsou připojeni. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Přihlášení správce virtuálního počítače](#virtual-machine-administrator-login) | Zobrazit virtuální počítače na portálu a přihlásit se jako správce | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Přispěvatel virtuálních počítačů](#virtual-machine-contributor) | Umožňuje spravovat virtuální počítače, ale ne přístup k nim, a ne virtuální síť nebo účet úložiště, ke kterým jsou připojeni. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Přihlášení uživatele virtuálního počítače](#virtual-machine-user-login) | Zobrazit virtuální počítače na portálu a přihlásit se jako běžný uživatel. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Sítě** |  |  |
> | [Přispěvatel koncového bodu CDN](#cdn-endpoint-contributor) | Může spravovat koncové body CDN, ale nemůže udělit přístup ostatním uživatelům. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Čtečka koncových bodů CDN](#cdn-endpoint-reader) | Můžete zobrazit koncové body CDN, ale nelze provést změny. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Přispěvatel profilu CDN](#cdn-profile-contributor) | Můžete spravovat profily CDN a jejich koncové body, ale nemůže udělit přístup ostatním uživatelům. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Čtečka profilů CDN](#cdn-profile-reader) | Můžete zobrazit profily CDN a jejich koncové body, ale nelze provádět změny. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Přispěvatel klasických sítí](#classic-network-contributor) | Umožňuje spravovat klasické sítě, ale ne přístup k nim. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Přispěvatel zóny DNS](#dns-zone-contributor) | Umožňuje spravovat zóny DNS a sady záznamů v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Přispěvatel sítě](#network-contributor) | Umožňuje spravovat sítě, ale ne přístup k nim. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Přispěvatel dopravního manažera](#traffic-manager-contributor) | Umožňuje spravovat profily traffic manageru, ale neumožňuje řídit, kdo k nim má přístup. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Úložiště** |  |  |
> | [Přispěvatel Avere](#avere-contributor) | Můžete vytvořit a spravovat avere vFXT clusteru. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Operátor Avere](#avere-operator) | Používá se ke správě clusteru Avere vFXT. | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Přispěvatel zálohy](#backup-contributor) | Umožňuje spravovat službu zálohování, ale nemůže vytvářet trezory a udělit přístup ostatním. | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operátor zálohování](#backup-operator) | Umožňuje spravovat služby zálohování, s výjimkou odebrání zálohy, vytváření trezoru a zpřístupnění ostatním. | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Čtečka záloh](#backup-reader) | Může zobrazit služby zálohování, ale nemůže provádět změny. | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Klasický přispěvatel účtu úložiště](#classic-storage-account-contributor) | Umožňuje spravovat klasické účty úložiště, ale ne přístup k nim. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Role služby operátora klíče klasického účtu úložiště](#classic-storage-account-key-operator-service-role) | Operátory klíčů klasického účtu úložiště mohou uvádět a regenerovat klíče na klasických účtech úložiště. | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Přispěvatel datové schránky](#data-box-contributor) | Umožňuje spravovat vše v části Služba datových schronů kromě přístupu k ostatním. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Čtečka datových schrápte](#data-box-reader) | Umožňuje spravovat službu Data Box Service s výjimkou vytváření podrobností objednávky nebo úprav objednávky a poskytování přístupu ostatním. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Vývojář analýzy datových jezer](#data-lake-analytics-developer) | Umožňuje odesílat, sledovat a spravovat vlastní úlohy, ale nevytvářet ani odstraňovat účty Data Lake Analytics. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Čtečka a přístup k datům](#reader-and-data-access) | Umožňuje zobrazit vše, ale neumožní vám odstranit nebo vytvořit účet úložiště nebo obsažený prostředek. Umožní také přístup pro čtení a zápis ke všem datům obsaženým v účtu úložiště prostřednictvím přístupu ke klíčům účtu úložiště. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Přispěvatel účtů úložiště](#storage-account-contributor) | Umožňuje správu účtů úložiště. Poskytuje přístup ke klíči účtu, který lze použít k přístupu k datům prostřednictvím autorizace sdíleného klíče. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Role služby operátoru účtu úložiště](#storage-account-key-operator-service-role) | Umožňuje výpis a obnovení přístupových klíčů účtu úložiště. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Přispěvatel dat objektu blob úložiště](#storage-blob-data-contributor) | Čtení, zápis a odstranění kontejnerů a objektů BLOB azure storage. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Vlastník dat objektu blob úložiště](#storage-blob-data-owner) | Poskytuje úplný přístup ke kontejnerům objektů blob azure storage a datům, včetně přiřazení řízení přístupu POSIX. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Čtečka dat objektu blob úložiště](#storage-blob-data-reader) | Přečtěte si a seznam kontejnerů azure storage a objektů BLOB. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Depatgator objektu blob úložiště](#storage-blob-delegator) | Získejte klíč delegování uživatele, který pak můžete použít k vytvoření sdíleného přístupového podpisu pro kontejner nebo objekt blob, který je podepsaný přihlašovacími údaji Azure AD. Další informace naleznete [v tématu Vytvoření uživatelskédelegace SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Přispěvatel sdílené složky úložiště SMB](#storage-file-data-smb-share-contributor) | Umožňuje čtení, zápis a odstranění přístupu k souborům nebo adresářům ve sdílených složkách Azure. Tato role nemá žádný vestavěný ekvivalent na souborových serverech systému Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Data úložiště dat SMB sdílet zvýšené přispěvatele](#storage-file-data-smb-share-elevated-contributor) | Umožňuje čtení, zápis, odstranění a úpravy seznamů AC v souborech nebo adresářích ve sdílených složkách Azure. Tato role je ekvivalentní sdílení souborů ACL změny na souborových serverech systému Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Čtečka sdílení dat souboru úložiště SMB](#storage-file-data-smb-share-reader) | Umožňuje přístup pro čtení v souborech nebo adresářích ve sdílených složkách Azure. Tato role je ekvivalentní sdílení souborů ACL čtení na souborových serverech systému Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Přispěvatel dat fronty úložiště](#storage-queue-data-contributor) | Čtení, zápis a odstranění front azure storage a zpráv fronty. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Procesor dat fronty úložiště](#storage-queue-data-message-processor) | Prohlédněte si, načtěte a odstraňte zprávu z fronty Azure Storage. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Odesílatel datových zpráv fronty úložiště](#storage-queue-data-message-sender) | Přidejte zprávy do fronty Azure Storage. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Čtečka dat fronty úložiště](#storage-queue-data-reader) | Přečtěte si a seznam front azure storage a zpráv fronty. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Čtečka dat Azure Maps (preview)](#azure-maps-data-reader-preview) | Uděluje přístup ke čtení map souvisejících dat z účtu mapy Azure. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Přispěvatel vyhledávací služby](#search-service-contributor) | Umožňuje spravovat vyhledávací služby, ale ne přístup k nim. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Přispěvatel webového plánu](#web-plan-contributor) | Umožňuje spravovat webové plány pro weby, ale ne přístup k nim. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Přispěvatel webových stránek](#website-contributor) | Umožňuje spravovat weby (nikoli webové plány), ale ne přístup k nim. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Kontejnery** |  |  |
> | [AcrDelete](#acrdelete) | acr odstranit | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | acr podepisující obrázek | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr vytáhnout | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | acr karanténní čtečka dat | cdda3590-29a3-44f6-95f2-9f980659eb04 cdda35904 cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | acr zapisovač dat karantény | c8d4ff99-41c3-41a8-9f60-21dfdad59608 c8d4ff99-41c3-41a8-9f60-21dfdad59608 c8d4ff99-41c3-41a8-9f60-21dfdad59608 c8 |
> | [Role správce clusteru Azure Kubernetes](#azure-kubernetes-service-cluster-admin-role) | Seznam akce pověření správce clusteru. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Role uživatele clusteru služby Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Seznam akce pověření uživatele clusteru. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253ff |
> | **Databáze** |  |  |
> | [Role čtečky účtů Cosmos DB](#cosmos-db-account-reader-role) | Umí číst data účtu Azure Cosmos DB. Informace o správě účtů Azure Cosmos DB najdete v [tématu DocumentDB Account Contributor.](#documentdb-account-contributor) | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operátor Cosmos DB](#cosmos-db-operator) | Umožňuje spravovat účty Azure Cosmos DB, ale ne přístup k datům v nich. Zabraňuje přístupu ke klíčům účtu a připojovacím řetězcům. | 230815da-be43-4aae-9cb4-875f7bd000aaa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Může odeslat žádost o obnovení pro databázi Cosmos DB nebo kontejner pro účet | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Přispěvatel účtu DocumentDB](#documentdb-account-contributor) | Můžete spravovat účty Azure Cosmos DB. Azure Cosmos DB se dříve označuje jako DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Přispěvatel mezipaměti Redis](#redis-cache-contributor) | Umožňuje spravovat mezipaměti Redis, ale ne přístup k nim. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Přispěvatel SQL DB](#sql-db-contributor) | Umožňuje spravovat databáze SQL, ale ne přístup k nim. Také nelze spravovat jejich zásady související se zabezpečením nebo jejich nadřazené servery SQL. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Přispěvatel spravované instance SQL](#sql-managed-instance-contributor) | Umožňuje spravovat spravované instance SQL a požadovanou konfiguraci sítě, ale nemůže udělit přístup ostatním. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Správce zabezpečení SQL](#sql-security-manager) | Umožňuje spravovat zásady související se zabezpečením serverů SQL a databází, ale nikoli k nim přístup. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Přispěvatel serveru SQL Server](#sql-server-contributor) | Umožňuje spravovat servery sql a databáze, ale nikoli přístup k nim, a nikoli jejich zásady související se zabezpečením. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analýza** |  |  |
> | [Vlastník dat centra událostí Azure](#azure-event-hubs-data-owner) | Umožňuje úplný přístup k prostředkům Centra událostí Azure. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Přijímač dat centra událostí Azure](#azure-event-hubs-data-receiver) | Umožňuje přístup k prostředkům Centra událostí Azure. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Odesílatel dat centra událostí Azure](#azure-event-hubs-data-sender) | Umožňuje odesílání přístupu k prostředkům Centra událostí Azure. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Přispěvatel datové továrny](#data-factory-contributor) | Vytvářejte a spravujte datové továrny a podřízené prostředky v nich. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Vychodění dat](#data-purger) | Dokáže vymazat analytická data | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Operátor clusteru HDInsight](#hdinsight-cluster-operator) | Umožňuje číst a upravovat konfigurace clusteru HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Přispěvatel doménových služeb HDInsight](#hdinsight-domain-services-contributor) | Může číst, vytvářet, upravovat a odstraňovat operace související s doménovými službami potřebné pro balíček HDInsight Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Přispěvatel Log Analytics](#log-analytics-contributor) | Přispěvatel log Analytics může číst všechna data monitorování a upravovat nastavení monitorování. Úpravy nastavení monitorování zahrnuje přidání rozšíření virtuálních počítače do virtuálních počítače; čtení klíčů účtu úložiště, abyste mohli konfigurovat kolekci protokolů z Azure Storage; vytváření a konfigurace účtů automatizace; přidávání řešení; a konfigurace diagnostiky Azure na všech prostředcích Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Čtenář Log Analytics](#log-analytics-reader) | Log Analytics Reader můžete zobrazit a prohledat všechna data monitorování, stejně jako a zobrazit nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Blockchain** |  |  |
> | [Přístup k uzlu člena blockchainu (náhled)](#blockchain-member-node-access-preview) | Umožňuje přístup k uzlům blockchainových členů | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + strojové učení** |  |  |
> | [Přispěvatel služeb cognitive services](#cognitive-services-contributor) | Umožňuje vytvářet, číst, aktualizovat, odstraňovat a spravovat klíče služeb Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Čtečka dat služeb Cognitive Services (preview)](#cognitive-services-data-reader-preview) | Umožňuje číst data služeb Cognitive Services. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Uživatel kognitivních služeb](#cognitive-services-user) | Umožňuje číst a seznam klíčů služby Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Smíšená realita** |  |  |
> | [Přispěvatel účtu prostorových ukotvení](#spatial-anchors-account-contributor) | Umožňuje spravovat prostorové kotvy ve vašem účtu, ale neodstraňovat je | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Vlastník účtu prostorových kotev](#spatial-anchors-account-owner) | Umožňuje spravovat prostorové kotvy ve vašem účtu, včetně jejich odstranění. | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Čtečka účtů prostorových kotev](#spatial-anchors-account-reader) | Umožňuje vyhledat a číst vlastnosti prostorových kotev ve vašem účtu. | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integrace** |  |  |
> | [Přispěvatel služby správy rozhraní API](#api-management-service-contributor) | Může spravovat služby a api | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Role operátora služby správy rozhraní API](#api-management-service-operator-role) | Může spravovat služby, ale ne api | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Role čtečky služby API Management Service](#api-management-service-reader-role) | Přístup jen pro čtení ke službě a řešením API | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Vlastník konfiguračních dat aplikace](#app-configuration-data-owner) | Umožňuje plný přístup k datům konfigurace aplikace. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Čtečka konfiguračních dat aplikace](#app-configuration-data-reader) | Umožňuje přístup pro čtení k datům konfigurace aplikace. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Vlastník dat azure service bus](#azure-service-bus-data-owner) | Umožňuje úplný přístup k prostředkům Azure Service Bus. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Přijímač dat azure service bus](#azure-service-bus-data-receiver) | Umožňuje přístup ke zdrojům Služby Azure Service Bus. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Odesílatel dat azure sběrnice](#azure-service-bus-data-sender) | Umožňuje odesílání přístupu k prostředkům Azure Service Bus. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Vlastník registrace zásobníku Azure](#azure-stack-registration-owner) | Umožňuje spravovat registrace Azure Stacku. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Přispěvatel eventgrideventového předplatného](#eventgrid-eventsubscription-contributor) | Umožňuje spravovat operace odběru událostí EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Čtečka eventgrid eventsubscriptionů](#eventgrid-eventsubscription-reader) | Umožňuje číst odběry událostí EventGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Přispěvatel účtu inteligentních systémů](#intelligent-systems-account-contributor) | Umožňuje spravovat účty intelligent systems, ale ne přístup k nim. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Přispěvatel aplikace logiky](#logic-app-contributor) | Umožňuje spravovat aplikace logiky, ale neměnit přístup k nim. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operátor aplikace logiky](#logic-app-operator) | Umožňuje číst, povolit a zakázat aplikace logiky, ale ne upravovat ani aktualizovat. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identita** |  |  |
> | [Přispěvatel spravované identity](#managed-identity-contributor) | Vytvořit, číst, aktualizovat a odstranit přiřazenou identitu uživatele | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operátor spravované identity](#managed-identity-operator) | Čtení a přiřazení přiřazené identity uživatele | f1a07417-d97a-45cb-824c-7a7477783830 |
> | **Zabezpečení** |  |  |
> | [Přispěvatel Azure Sentinelu](#azure-sentinel-contributor) | Přispěvatel Azure Sentinelu | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Čtečka Azure Sentinel](#azure-sentinel-reader) | Čtečka Azure Sentinel | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel respondér](#azure-sentinel-responder) | Azure Sentinel respondér | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Přispěvatel trezoru klíčů](#key-vault-contributor) | Umožňuje spravovat trezory klíčů, ale ne přístup k nim. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Správce zabezpečení](#security-admin) | Umí zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení, zavřít výstrahy a doporučení. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Přispěvatel hodnocení zabezpečení](#security-assessment-contributor) | Umožňuje nabízení hodnocení do centra zabezpečení. | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Správce zabezpečení (starší verze)](#security-manager-legacy) | Toto je starší role. Místo toho použijte správce zabezpečení. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Čtečka zabezpečení](#security-reader) | Umí zobrazit doporučení a výstrahy, zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, ale nemůže provádět změny. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Uživatel devTest Labs](#devtest-labs-user) | Umožňuje připojit, spustit, restartovat a vypnout virtuální počítače ve svých laboratořích Azure DevTest. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Lab Tvůrce](#lab-creator) | Umožňuje vytvářet, spravovat a odstraňovat spravované laboratoře v rámci účtů Azure Lab. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitorování** |  |  |
> | [Přispěvatel komponenty Přehledy aplikací](#application-insights-component-contributor) | Umožňuje spravovat komponenty Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Ladicí program snímku přehledů aplikací](#application-insights-snapshot-debugger) | Poskytuje uživateli oprávnění k zobrazení a stažení ladicích snímků shromážděných pomocí ladicího programu snímek Fonýře přehledů aplikací. Všimněte si, že tato oprávnění nejsou zahrnuty v rolích [vlastníka](#owner) nebo [přispěvatele.](#contributor) | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Přispěvatel monitorování](#monitoring-contributor) | Dokáže číst všechna data monitorování a upravovat nastavení monitorování. Viz taky [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Vydavatel metrik monitorování](#monitoring-metrics-publisher) | Umožňuje metriky publikování oproti prostředkům Azure. | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Monitorovací čtečka](#monitoring-reader) | Umí číst všechna data monitorování (metriky, protokoly atd.). Viz taky [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Přispěvatel sešitu](#workbook-contributor) | Sdílené sešity lze uložit. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Čtečka sešitů](#workbook-reader) | Umí číst sešity. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Management + řízení** |  |  |
> | [Automatizace Obsluha úloh](#automation-job-operator) | Vytvářejte a spravujte úlohy pomocí runbooků automatizace. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automatizace Obsluha](#automation-operator) | Automatizační operátoři mohou zahajovat, zastavovat, pozastavovat a pokračovat v práci | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automatizace Runbook Operátor](#automation-runbook-operator) | Přečtěte si vlastnosti runbooku – abyste mohli vytvářet úlohy runbooku. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Připojení připojeného počítače Azure](#azure-connected-machine-onboarding) | Můžete na palubě připojených počítačů Azure. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Správce prostředků připojeného počítače Azure](#azure-connected-machine-resource-administrator) | Můžete číst, zapisovat, odstraňovat a znovu nastoupit na Azure Connected Machines. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Čtenář fakturace](#billing-reader) | Umožňuje přístup pro čtení k fakturačním údajům | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Přispěvatel podrobného plánu](#blueprint-contributor) | Můžete spravovat definice podrobného plánu, ale ne přiřazovat je. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Operátor plánu](#blueprint-operator) | Můžete přiřadit existující publikované podrobné plány, ale nelze vytvořit nové podrobné plány. Všimněte si, že to funguje pouze v případě, že přiřazení se provádí s uživatelem přiřazenou spravovanou identitou. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Přispěvatel pro řízení nákladů](#cost-management-contributor) | Dokáže zobrazit náklady a spravovat konfiguraci nákladů (např. rozpočty, exporty) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Čtečka správy nákladů](#cost-management-reader) | Může zobrazit údaje o nákladech a konfiguraci (např. rozpočty, exporty) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Role přispěvatele spravované aplikace](#managed-application-contributor-role) | Umožňuje vytváření prostředků spravovaných aplikací. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Role operátoru spravované aplikace](#managed-application-operator-role) | Umožňuje číst a provádět akce s prostředky spravovaných aplikací. | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Čtečka spravovaných aplikací](#managed-applications-reader) | Umožňuje číst prostředky ve spravované aplikaci a požádat o přístup k JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Přiřazení registrace spravovaných služeb Odstranit roli](#managed-services-registration-assignment-delete-role) | Role odstranění registrace spravovaných služeb umožňuje správě uživatelů klienta odstranit přiřazení registrace přiřazené jejich tenantovi. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Přispěvatel skupiny pro správu](#management-group-contributor) | Role přispěvatele skupiny pro správu | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Čtečka skupin pro správu](#management-group-reader) | Role čtečky skupiny pro správu | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Nový přispěvatel účtu Relic APM](#new-relic-apm-account-contributor) | Umožňuje spravovat nové účty a aplikace správy výkonu aplikací, ale nikoli k nim přístup. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Zapisovač dat přehledů zásad (náhled)](#policy-insights-data-writer-preview) | Umožňuje přístup pro čtení k zásadám prostředků a zápis u událostí zásad součástí prostředků. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Přispěvatel zásad prostředků](#resource-policy-contributor) | Uživatelé s právy k vytváření nebo úpravám zásad prostředků, vytváření lístku podpory a čtení prostředků/hierarchie. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Přispěvatel obnovení webu](#site-recovery-contributor) | Umožňuje spravovat službu Site Recovery s výjimkou vytváření trezoru a přiřazení rolí. | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operátor obnovení webu](#site-recovery-operator) | Umožňuje převzetí služeb při selhání a navrácení služeb po obnovení služeb při selhání, ale neprovádět jiné operace správy obnovení webu. | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Čtečka obnovení webu](#site-recovery-reader) | Umožňuje zobrazit stav Obnovení webu, ale neprovádět jiné operace správy. | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Přispěvatel žádosti o podporu](#support-request-contributor) | Umožňuje vytvářet a spravovat žádosti o podporu. | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Přispěvatel značky](#tag-contributor) | Umožňuje spravovat značky na entitách, aniž by poskytoval přístup k entitám samotným. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Další** |  |  |
> | [BizTalk přispěvatel](#biztalk-contributor) | Umožňuje spravovat BizTalk služby, ale ne přístup k nim. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Přispěvatel kolekcí úloh plánovače](#scheduler-job-collections-contributor) | Umožňuje spravovat kolekce úloh plánovače, ale ne přístup k nim. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Obecné


### <a name="contributor"></a>Přispěvatel

Umožňuje spravovat vše kromě udělení přístupu k prostředkům.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | * | Vytváření a správa prostředků všech typů |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Odstranění rolí, přiřazení zásad, definic zásad a definic sad zásad |
> | Microsoft.Authorization/*/Write | Vytvoření rolí, přiřazení rolí, přiřazení zásad, definic zásad a definic sad zásad |
> | Microsoft.Authorization/elevateAccess/Action | Udělit volajícímu přístup Správce uživatelských přístupů v oboru tenanta |
> | Microsoft.Blueprint/blueprintÚkoly/zápis | Vytvoření nebo aktualizace všech přiřazení podrobného plánu |
> | Microsoft.Blueprint/blueprintÚkoly/odstranění | Odstranění všech přiřazení podrobného plánu |
> | **Akce data** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
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

Umožňuje spravovat vše, včetně přístupu k prostředkům.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | * | Vytváření a správa prostředků všech typů |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything, including access to resources.",
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

Umožňuje zobrazit vše, ale neprovádět žádné změny.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | */čtení | Přečtěte si prostředky všech typů, s výjimkou tajných kódů. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything, but not make any changes.",
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

### <a name="user-access-administrator"></a>Správce přístupu uživatelů

Umožňuje spravovat přístup uživatelů k prostředkům Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | */čtení | Přečtěte si prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.Authorization/* | Správa autorizace |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

## <a name="compute"></a>Služba Compute


### <a name="classic-virtual-machine-contributor"></a>Klasický přispěvatel virtuálního počítače

Umožňuje spravovat klasické virtuální počítače, ale ne přístup k nim, a ne virtuální síť nebo účet úložiště, ke kterým jsou připojeni.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.ClassicCompute/domainNames/* | Vytváření a správa klasických názvů výpočetních domén |
> | Microsoft.ClassicCompute/virtualMachines/* | Vytváření a správu virtuálních počítačů |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Propojení vyhrazené ip adresy |
> | Microsoft.ClassicNetwork/reservedIps/read | Získá vyhrazené Ips |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Připojí se k virtuální síti. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Získejte virtuální síť. |
> | Microsoft.ClassicStorage/storageAccounts/disky/čtení | Vrátí disk účtu úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Vrátí bitovou kopii účtu úložiště. (Zastaralé. Použijte 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Zobrazí seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/read | Vraťte účet úložiště s daným účtem. |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Zobrazit virtuální počítače na portálu a přihlásit se jako správce

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Network/publicIPAdresy/čtení | Získá definici veřejné IP adresy. |
> | Microsoft.Network/virtualNetworks/read | Získání definice virtuální sítě |
> | Microsoft.Network/loadBalancers/čtení | Získá definici systému vyrovnávání zatížení. |
> | Microsoft.Network/networkInterfaces/read | Získá definici síťového rozhraní.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Compute/virtualMachines/login/action | Přihlášení k virtuálnímu počítači jako běžný uživatel |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Přihlášení k virtuálnímu počítači pomocí oprávnění správce systému Windows nebo root uživatele |
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

Umožňuje spravovat virtuální počítače, ale ne přístup k nim, a ne virtuální síť nebo účet úložiště, ke kterým jsou připojeni.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Compute/availabilitySets/* | Vytváření a správa sad výpočetní dostupnosti |
> | Microsoft.Compute/locations/* | Vytváření a správa výpočetních umístění |
> | Microsoft.Compute/virtualMachines/* | Vytváření a správu virtuálních počítačů |
> | Microsoft.Compute/virtualMachineScaleSets/* | Vytváření a správu škálovacích sad virtuálních počítačů |
> | Microsoft.Compute/disky/zápis | Vytvoří nový disk nebo aktualizuje existující disk. |
> | Microsoft.Compute/disky/čtení | Získání vlastností disku |
> | Microsoft.Compute/disky/delete | Odstraní disk |
> | Microsoft.DevTestLab/plány/* |  |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Připojí fond back-endových adres brány aplikace. Nelze upozornit. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Připojí se k fondu back-endových adres vykladače vyrovnávání zatížení. Nelze upozornit. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Připojí se k příchozímu fondu NAT pro vyrovnávání zatížení. Nelze upozornit. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Připojí se k pravidlu příchozího nat ovače zatížení. Nelze upozornit. |
> | Microsoft.Network/loadBalancers/sondy/join/action | Umožňuje použití sond likátoru zatížení. Například s tímto oprávněním healthProbe vlastnost škálovací sady virtuálního zařízení můžete odkazovat na sondu. Nelze upozornit. |
> | Microsoft.Network/loadBalancers/čtení | Získá definici systému vyrovnávání zatížení. |
> | Microsoft.Network/locations/* | Vytváření a správa síťových umístění |
> | Microsoft.Network/networkInterfaces/* | Vytváření a správa síťových rozhraní |
> | Microsoft.Network/networkSecurityGroups/join/action | Připojí se ke skupině zabezpečení sítě. Nelze upozornit. |
> | Microsoft.Network/networkSecurityGroups/read | Získá definici skupiny zabezpečení sítě. |
> | Microsoft.Network/publicIPAdresy/připojit/akce | Připojí se k veřejné IP adrese. Nelze upozornit. |
> | Microsoft.Network/publicIPAdresy/čtení | Získá definici veřejné IP adresy. |
> | Microsoft.Network/virtualNetworks/read | Získání definice virtuální sítě |
> | Microsoft.Network/virtualNetworks/podsítě/join/action | Připojí se k virtuální síti. Nelze upozornit. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Vytvoření záměru ochrany zálohy |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti o objektu chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Vytvoření zálohové chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Vytvoří zásady ochrany |
> | Microsoft.RecoveryServices/Vaults/read | Operace Získat úložiště získá objekt představující prostředek Azure typu trezor |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti o využití úložiště služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/write | Vytvořit operaci úložiště vytvoří prostředek Azure typu trezor. |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Zobrazit virtuální počítače na portálu a přihlásit se jako běžný uživatel.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Network/publicIPAdresy/čtení | Získá definici veřejné IP adresy. |
> | Microsoft.Network/virtualNetworks/read | Získání definice virtuální sítě |
> | Microsoft.Network/loadBalancers/čtení | Získá definici systému vyrovnávání zatížení. |
> | Microsoft.Network/networkInterfaces/read | Získá definici síťového rozhraní.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Compute/virtualMachines/login/action | Přihlášení k virtuálnímu počítači jako běžný uživatel |
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

Může spravovat koncové body CDN, ale nemůže udělit přístup ostatním uživatelům.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/koncové body/* |  |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="cdn-endpoint-reader"></a>Čtečka koncových bodů CDN

Můžete zobrazit koncové body CDN, ale nelze provést změny.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/koncové body/*/čtení |  |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Můžete spravovat profily CDN a jejich koncové body, ale nemůže udělit přístup ostatním uživatelům.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Můžete zobrazit profily CDN a jejich koncové body, ale nelze provádět změny.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Umožňuje spravovat klasické sítě, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.ClassicNetwork/* | Vytváření a správa klasických sítí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Umožňuje spravovat zóny DNS a sady záznamů v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Network/dnsZones/* | Vytváření a správa zón a záznamů DNS |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Network/* | Vytváření a správa sítí |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="traffic-manager-contributor"></a>Přispěvatel dopravního manažera

Umožňuje spravovat profily traffic manageru, ale neumožňuje řídit, kdo k nim má přístup.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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


### <a name="avere-contributor"></a>Přispěvatel Avere

Můžete vytvořit a spravovat avere vFXT clusteru.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disky/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Získání definice virtuální sítě |
> | Microsoft.Network/virtualNetworks/podsítě/čtení | Získá definici podsítě virtuální sítě. |
> | Microsoft.Network/virtualNetworks/podsítě/join/action | Připojí se k virtuální síti. Nelze upozornit. |
> | Microsoft.Network/virtualNetworks/podsítě/joinViaServiceEndpoint/action | Připojí prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozornit. |
> | Microsoft.Network/networkSecurityGroups/join/action | Připojí se ke skupině zabezpečení sítě. Nelze upozornit. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* | Vytvoření a správa účtů úložiště |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Získá prostředky pro skupinu prostředků. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Vrátí výsledek odstranění objektu blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů BLOB. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Vrátí výsledek zápisu do objektu blob. |
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

### <a name="avere-operator"></a>Operátor Avere

Používá se ke správě clusteru Avere vFXT.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Compute/virtualMachines/read | Získání vlastností virtuálního počítače |
> | Microsoft.Network/networkInterfaces/read | Získá definici síťového rozhraní.  |
> | Microsoft.Network/networkInterfaces/write | Vytvoří síťové rozhraní nebo aktualizuje existující síťové rozhraní.  |
> | Microsoft.Network/virtualNetworks/read | Získání definice virtuální sítě |
> | Microsoft.Network/virtualNetworks/podsítě/čtení | Získá definici podsítě virtuální sítě. |
> | Microsoft.Network/virtualNetworks/podsítě/join/action | Připojí se k virtuální síti. Nelze upozornit. |
> | Microsoft.Network/networkSecurityGroups/join/action | Připojí se ke skupině zabezpečení sítě. Nelze upozornit. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Vrátí výsledek odstranění kontejneru. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Vrátí seznam kontejnerů |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Vrátí výsledek kontejneru objektů blob. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Vrátí výsledek odstranění objektu blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů BLOB. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Vrátí výsledek zápisu do objektu blob. |
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

### <a name="backup-contributor"></a>Přispěvatel zálohy

Umožňuje spravovat službu zálohování, ale nemůže vytvářet trezory a udělit přístup ostatním.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Network/virtualNetworks/read | Získání definice virtuální sítě |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Správa výsledků operace při správě zálohování |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Vytvoření a správa záložních kontejnerů uvnitř záložních prostředků prostředků prostředků úložiště služby Recovery Services |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aktualizuje seznam kontejnerů. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Vytváření a správa úloh zálohování |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export úlohy |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Vytváření a správa výsledků operací správy zálohování |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Vytváření a správa zásad zálohování |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Vytváření a správa položek, které lze zálohovat |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Vytváření a správa zálohovaných položek |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Vytváření a správa kontejnerů s položkami zálohování |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageShrnutí/čtení | Vrátí souhrny chráněných položek a chráněných serverů pro službu pro obnovení . |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Vytvoření a správa certifikátů souvisejících se zálohováním v trezoru služby Recovery Services |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Vytváření a správa rozšířených informací týkajících se trezoru |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb obnovení. |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfigurace/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Operace Získat úložiště získá objekt představující prostředek Azure typu trezor |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Vytváření a správa registrovaných identit |
> | Microsoft.RecoveryServices/Vaults/usages/* | Vytvoření a správa využití trezoru služby Recovery Services |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Ověřit operaci chráněné položky |
> | Microsoft.RecoveryServices/Vaults/write | Vytvořit operaci úložiště vytvoří prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Vrátí stav operace zálohování pro úložiště služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Vrátí všechny servery pro správu zálohování registrované v úložišti. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Získejte všechny chránitelné kontejnery |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrola stavu zálohování trezorů služby Recovery Services |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Ověřit funkce |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Vyřešuje výstrahu. |
> | Microsoft.RecoveryServices/operations/read | Operace vrátí seznam operací pro zprostředkovatele prostředků. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Získá stav operace pro danou operaci. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Vypsat všechny záměry ochrany zálohování |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="backup-operator"></a>Operátor zálohování

Umožňuje spravovat služby zálohování, s výjimkou odebrání zálohy, vytváření trezoru a zpřístupnění ostatním.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Network/virtualNetworks/read | Získání definice virtuální sítě |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Vrátí stav operace. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Získá výsledek operace provedené na kontejneru ochrany. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Provádí zálohování pro chráněnou položku. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Získá výsledek operace provedené na chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Vrátí stav operace prováděné u chráněných položek. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti o objektu chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Zřídit okamžité obnovení položek pro chráněnou položku |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Získejte body obnovení pro chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Obnovení bodů obnovení pro chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odvolat okamžité obnovení položky pro chráněnou položku |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Vytvoření zálohové chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Vrátí všechny registrované kontejnery. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aktualizuje seznam kontejnerů. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Vytváření a správa úloh zálohování |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export úlohy |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Vytváření a správa výsledků operací správy zálohování |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Získejte výsledky operace zásad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany. |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Vytváření a správa položek, které lze zálohovat |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Vrátí všechny kontejnery, které patří do předplatného. |
> | Microsoft.RecoveryServices/Vaults/backupUsageShrnutí/čtení | Vrátí souhrny chráněných položek a chráněných serverů pro službu pro obnovení . |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operace Aktualizovat certifikát prostředku aktualizuje certifikát pověření prostředku nebo trezoru. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu představující prostředek Azure typu ?trezor? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operace Získat rozšířené informace získá rozšířené informace o objektu představující prostředek Azure typu ?trezor? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb obnovení. |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfigurace/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Operace Získat úložiště získá objekt představující prostředek Azure typu trezor |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operace Získat výsledky operace lze použít získat stav operace a výsledek pro asynchronně odeslanou operaci. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnery operace lze získat kontejnery registrované pro prostředek. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operace kontejneru služby registrace lze zaregistrovat kontejner se službou obnovení. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti o využití úložiště služby Recovery Services. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Ověřit operaci chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Vrátí stav operace zálohování pro úložiště služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Získat stav operace zásad. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Vytvoří registrovaný kontejner. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Dotaz na úlohy v rámci kontejneru |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Vrátí všechny servery pro správu zálohování registrované v úložišti. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Vytvoření záměru ochrany zálohy |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Získání záměru ochrany zálohy |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Získejte všechny chránitelné kontejnery |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Získání všech položek v kontejneru |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrola stavu zálohování trezorů služby Recovery Services |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Ověřit funkce |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Vyřešuje výstrahu. |
> | Microsoft.RecoveryServices/operations/read | Operace vrátí seznam operací pro zprostředkovatele prostředků. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Získá stav operace pro danou operaci. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Vypsat všechny záměry ochrany zálohování |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="backup-reader"></a>Čtečka záloh

Může zobrazit služby zálohování, ale nemůže provádět změny.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace používaná službou |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Vrátí stav operace. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Získá výsledek operace provedené na kontejneru ochrany. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Získá výsledek operace provedené na chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Vrátí stav operace prováděné u chráněných položek. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti o objektu chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Získejte body obnovení pro chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Vrátí všechny registrované kontejnery. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Vrátí výsledek operace úlohy. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Vrátí všechny objekty úlohy. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export úlohy |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Vrátí výsledek operace zálohování pro úložiště služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Získejte výsledky operace zásad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Vrátí všechny kontejnery, které patří do předplatného. |
> | Microsoft.RecoveryServices/Vaults/backupUsageShrnutí/čtení | Vrátí souhrny chráněných položek a chráněných serverů pro službu pro obnovení . |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu představující prostředek Azure typu ?trezor? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb obnovení. |
> | Microsoft.RecoveryServices/Vaults/read | Operace Získat úložiště získá objekt představující prostředek Azure typu trezor |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operace Získat výsledky operace lze použít získat stav operace a výsledek pro asynchronně odeslanou operaci. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnery operace lze získat kontejnery registrované pro prostředek. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Vrátí konfiguraci úložiště pro úložiště služby Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Vrátí konfiguraci úložiště služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Vrátí stav operace zálohování pro úložiště služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Získat stav operace zásad. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Vrátí všechny servery pro správu zálohování registrované v úložišti. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Získání záměru ochrany zálohy |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Získání všech položek v kontejneru |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrola stavu zálohování trezorů služby Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfigurace/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Vyřešuje výstrahu. |
> | Microsoft.RecoveryServices/operations/read | Operace vrátí seznam operací pro zprostředkovatele prostředků. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Získá stav operace pro danou operaci. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Vypsat všechny záměry ochrany zálohování |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti o využití úložiště služby Recovery Services. |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Ověřit funkce |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="classic-storage-account-contributor"></a>Klasický přispěvatel účtu úložiště

Umožňuje spravovat klasické účty úložiště, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.ClassicStorage/storageAccounts/* | Vytvoření a správa účtů úložiště |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Operátory klíčů klasického účtu úložiště mohou uvádět a regenerovat klíče na klasických účtech úložiště.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Zobrazí seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Znovu vygeneruje existující přístupové klíče pro účet úložiště. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="data-box-contributor"></a>Přispěvatel datové schránky

Umožňuje spravovat vše v části Služba datových schronů kromě přístupu k ostatním.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="data-box-reader"></a>Čtečka datových schrápte

Umožňuje spravovat službu Data Box Service s výjimkou vytváření podrobností objednávky nebo úprav objednávky a poskytování přístupu ostatním.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Databox/*/čti |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action Microsoft.Databox/jobs/listcredentials/action Microsoft.Databox/jobs/listcredentials/action Microsoft. | Zobrazí seznam nešifrovaných pověření souvisejících s objednávkou. |
> | Microsoft.Databox/locations/availableSkus/action | Tato metoda vrátí seznam dostupných skus. |
> | Microsoft.Databox/locations/validateInputs/action | Tato metoda provádí všechny typy ověření. |
> | Microsoft.Databox/locations/regionKonfigurace/akce | Tato metoda vrátí konfigurace pro oblast. |
> | Microsoft.Databox/locations/validateAddress/action | Ověří dodací adresu a případně zadává alternativní adresy. |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="data-lake-analytics-developer"></a>Vývojář analýzy datových jezer

Umožňuje odesílat, sledovat a spravovat vlastní úlohy, ale nevytvářet ani odstraňovat účty Data Lake Analytics.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.BigAnalytics/účty/* |  |
> | Microsoft.DataLakeAnalytics/účty/* |  |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/účty/Odstranit |  |
> | Microsoft.BigAnalytics/účty/TakeOwnership/akce |  |
> | Microsoft.BigAnalytics/účty/Zápis |  |
> | Microsoft.DataLakeAnalytics/účty/Odstranit | Odstraňte účet DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/účty/TakeOwnership/action | Udělte oprávnění ke zrušení úloh odeslaných jinými uživateli. |
> | Microsoft.DataLakeAnalytics/účty/Zápis | Vytvořte nebo aktualizujte účet DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Vytvořte nebo aktualizujte propojený účet DataLakeStore účtu DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/účty/dataLakeStoreAccounts/Delete | Odpojte účet DataLakeStore od účtu DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Vytvořte nebo aktualizujte propojený účet úložiště účtu DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/účty/úložištěÚčty/Odstranit | Odpojte účet úložiště od účtu DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Vytvořte nebo aktualizujte pravidlo brány firewall. |
> | Microsoft.DataLakeAnalytics/účty/firewallPravidla/Odstranění | Odstraňte pravidlo brány firewall. |
> | Microsoft.DataLakeAnalytics/účty/computePolicies/Zápis | Vytvořte nebo aktualizujte zásady výpočtu. |
> | Microsoft.DataLakeAnalytics/účty/computePolicies/Delete | Odstraňte zásady výpočtu. |
> | **Akce data** |  |
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

### <a name="reader-and-data-access"></a>Čtečka a přístup k datům

Umožňuje zobrazit vše, ale neumožní vám odstranit nebo vytvořit účet úložiště nebo obsažený prostředek. Umožní také přístup pro čtení a zápis ke všem datům obsaženým v účtu úložiště prostřednictvím přístupu ke klíčům účtu úložiště.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | Vrátí token SAS účtu pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Umožňuje správu účtů úložiště. Poskytuje přístup ke klíči účtu, který lze použít k přístupu k datům prostřednictvím autorizace sdíleného klíče.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo přečte diagnostické nastavení pro server Analysis Server. |
> | Microsoft.Network/virtualNetworks/podsítě/joinViaServiceEndpoint/action | Připojí prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozornit. |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Storage/storageAccounts/* | Vytvoření a správa účtů úložiště |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="storage-account-key-operator-service-role"></a>Role služby operátoru účtu úložiště

Umožňuje výpis a obnovení přístupových klíčů účtu úložiště.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Znovu vygeneruje přístupové klíče pro zadaný účet úložiště. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="storage-blob-data-contributor"></a>Přispěvatel dat objektu blob úložiště

Čtení, zápis a odstranění kontejnerů a objektů BLOB azure storage. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Odstraňte kontejner. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Vraťte kontejner nebo seznam kontejnerů. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Upravte metadata nebo vlastnosti kontejneru. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Vrátí klíč delegování uživatele pro službu blob. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Odstraňte objekt blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů BLOB. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Napište do objektu blob. |
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

### <a name="storage-blob-data-owner"></a>Vlastník dat objektu blob úložiště

Poskytuje úplný přístup ke kontejnerům objektů blob azure storage a datům, včetně přiřazení řízení přístupu POSIX. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Úplná oprávnění pro kontejnery. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Vrátí klíč delegování uživatele pro službu blob. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Úplná oprávnění pro objekty BLOB. |
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

### <a name="storage-blob-data-reader"></a>Čtečka dat objektu blob úložiště

Přečtěte si a seznam kontejnerů azure storage a objektů BLOB. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Vraťte kontejner nebo seznam kontejnerů. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Vrátí klíč delegování uživatele pro službu blob. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů BLOB. |
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

### <a name="storage-blob-delegator"></a>Depatgator objektu blob úložiště

Získejte klíč delegování uživatele, který pak můžete použít k vytvoření sdíleného přístupového podpisu pro kontejner nebo objekt blob, který je podepsaný přihlašovacími údaji Azure AD. Další informace naleznete [v tématu Vytvoření uživatelskédelegace SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Vrátí klíč delegování uživatele pro službu blob. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="storage-file-data-smb-share-contributor"></a>Přispěvatel sdílené složky úložiště SMB

Umožňuje čtení, zápis a odstranění přístupu k souborům nebo adresářům ve sdílených složkách Azure. Tato role nemá žádný vestavěný ekvivalent na souborových serverech systému Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Vrátí soubor nebo složku nebo seznam souborů/složek. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Vrátí výsledek zápisu souboru nebo vytvoření složky. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Vrátí výsledek odstranění souboru nebo složky. |
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

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Data úložiště dat SMB sdílet zvýšené přispěvatele

Umožňuje čtení, zápis, odstranění a úpravy seznamů AC v souborech nebo adresářích ve sdílených složkách Azure. Tato role je ekvivalentní sdílení souborů ACL změny na souborových serverech systému Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Vrátí soubor nebo složku nebo seznam souborů/složek. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Vrátí výsledek zápisu souboru nebo vytvoření složky. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Vrátí výsledek odstranění souboru nebo složky. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Vrátí výsledek úpravy oprávnění k souboru nebo složce. |
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

### <a name="storage-file-data-smb-share-reader"></a>Čtečka sdílení dat souboru úložiště SMB

Umožňuje přístup pro čtení v souborech nebo adresářích ve sdílených složkách Azure. Tato role je ekvivalentní sdílení souborů ACL čtení na souborových serverech systému Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Vrátí soubor nebo složku nebo seznam souborů/složek. |
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

Čtení, zápis a odstranění front azure storage a zpráv fronty. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Odstraňte frontu. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Vrátí frontu nebo seznam front. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Upravte metadata nebo vlastnosti fronty. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Odstraňte jednu nebo více zpráv z fronty. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Prohlédněte nebo načtěte jednu nebo více zpráv z fronty. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Přidejte zprávu do fronty. |
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

### <a name="storage-queue-data-message-processor"></a>Procesor dat fronty úložiště

Prohlédněte si, načtěte a odstraňte zprávu z fronty Azure Storage. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Nahlédněte do zprávy. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Načíst a odstranit zprávu. |
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

### <a name="storage-queue-data-message-sender"></a>Odesílatel datových zpráv fronty úložiště

Přidejte zprávy do fronty Azure Storage. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Přidejte zprávu do fronty. |
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

Přečtěte si a seznam front azure storage a zpráv fronty. Informace o tom, které akce jsou požadovány pro danou datovou operaci, naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Vrátí frontu nebo seznam front. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Prohlédněte nebo načtěte jednu nebo více zpráv z fronty. |
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


### <a name="azure-maps-data-reader-preview"></a>Čtečka dat Azure Maps (preview)

Uděluje přístup ke čtení map souvisejících dat z účtu mapy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Maps/účty/data/čtení | Uděluje datům přístup pro čtení k účtu mapy. |
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
        "Microsoft.Maps/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Přispěvatel vyhledávací služby

Umožňuje spravovat vyhledávací služby, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Search/searchServices/* | Vytváření a správa vyhledávacích služeb |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="web-plan-contributor"></a>Přispěvatel webového plánu

Umožňuje spravovat webové plány pro weby, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Web/serverFarms/* | Vytvoření a správa serverových farem |
> | Microsoft.Web/hostingProstředí/Připojit se/Akce | Připojí se k prostředí služby App Service |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="website-contributor"></a>Přispěvatel webových stránek

Umožňuje spravovat weby (nikoli webové plány), ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Insights/komponenty/* | Vytváření a správa komponent Přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Web/certifikáty/* | Vytváření a správa certifikátů webu |
> | Microsoft.Web/listSitesAssignedToHostName/read | Získejte názvy webů přiřazených názvu hostitele. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Získání vlastností v plánu služby App Service |
> | Microsoft.Web/weby/* | Vytváření a správa webů (vytvoření webu také vyžaduje oprávnění k zápisu do přidruženého plánu služby App Service) |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

acr odstranit

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registry/artefakty/odstranění | Odstraňte artefakt v registru kontejneru. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

acr podepisující obrázek

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registry/sign/write | Nabízená nebo vyžádat obsah důvěryhodnost metadata pro registru kontejneru. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

acr vytáhnout

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registry/pull/read | Vyžádat nebo získat image z registru kontejneru. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

acr push

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registry/pull/read | Vyžádat nebo získat image z registru kontejneru. |
> | Microsoft.ContainerRegistry/registry/push/write | Nabízená nebo zapsat bitové kopie do registru kontejneru. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

acr karanténní čtečka dat

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registry/quarantine/read | Vyžádat nebo získat obrázky v karanténě z registru kontejnerů |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

acr zapisovač dat karantény

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registry/quarantine/read | Vyžádat nebo získat obrázky v karanténě z registru kontejnerů |
> | Microsoft.ContainerRegistry/registry/quarantine/write | Zápis/úprava karanténního stavu obrázků v karanténě |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Role správce clusteru Azure Kubernetes

Seznam akce pověření správce clusteru.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Seznam pověření clusteruSprávce spravovaného clusteru |
> | Microsoft.ContainerService/managedClusters/accessProfily/listCredential/action | Získání profilu přístupu ke spravovanému clusteru podle názvu role pomocí pověření seznamu |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action"
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

Seznam akce pověření uživatele clusteru.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Seznam clusteruPověření uživatele spravovaného clusteru |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
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

## <a name="databases"></a>Databáze


### <a name="cosmos-db-account-reader-role"></a>Role čtečky účtů Cosmos DB

Umí číst data účtu Azure Cosmos DB. Informace o správě účtů Azure Cosmos DB najdete v [tématu DocumentDB Account Contributor.](#documentdb-account-contributor)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.DocumentDB/*/čti | Přečtěte si libovolnou sbírku |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Přečte klíče pouze pro čtení databázového účtu. |
> | Microsoft.Insights/MetricDefinitions/čtení | Čtení definic metrik |
> | Microsoft.Insights/Metriky/čtení | Číst metriky |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Umožňuje spravovat účty Azure Cosmos DB, ale ne přístup k datům v nich. Zabraňuje přístupu ke klíčům účtu a připojovacím řetězcům.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Network/virtualNetworks/podsítě/joinViaServiceEndpoint/action | Připojí prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozornit. |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **Akce data** |  |
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

Může odeslat žádost o obnovení pro databázi Cosmos DB nebo kontejner pro účet

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Odeslání požadavku na konfiguraci zálohování |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Odeslání žádosti o obnovení |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Můžete spravovat účty Azure Cosmos DB. Azure Cosmos DB se dříve označuje jako DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.DocumentDb/databaseAccounts/* | Vytváření a správa účtů Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Network/virtualNetworks/podsítě/joinViaServiceEndpoint/action | Připojí prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozornit. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="redis-cache-contributor"></a>Přispěvatel mezipaměti Redis

Umožňuje spravovat mezipaměti Redis, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Cache/redis/* | Vytváření a správa mezipamětí Redis |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="sql-db-contributor"></a>Přispěvatel SQL DB

Umožňuje spravovat databáze SQL, ale ne přístup k nim. Také nelze spravovat jejich zásady související se zabezpečením nebo jejich nadřazené servery SQL.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servery/databáze/* | Vytvoření a správa databází SQL |
> | Microsoft.Sql/servery/čtení | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Insights/metriky/čtení | Číst metriky |
> | Microsoft.Insights/metricDefinitions/read | Čtení definic metrik |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servery/databáze/auditingZásady/* | Upravit zásady auditu |
> | Microsoft.Sql/servery/databáze/auditingSettings/* | Upravit nastavení auditu |
> | Microsoft.Sql/servers/databases/auditRecords/read | Načtení záznamů auditu objektů blob databáze |
> | Microsoft.Sql/servery/databáze/connectionPolicies/* | Úprava zásad připojení |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servery/databáze/dataMaskingPolicies/* | Úprava zásad maskování dat |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servery/databáze/securityAlertPolicies/* | Úprava zásad výstrah zabezpečení |
> | Microsoft.Sql/servery/databáze/securityMetrics/* | Úpravy metrik zabezpečení |
> | Microsoft.Sql/servery/databáze/citlivostLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **Akce data** |  |
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
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
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

Umožňuje spravovat spravované instance SQL a požadovanou konfiguraci sítě, ale nemůže udělit přístup ostatním.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Network/virtualNetworks/podsítě/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Insights/metriky/čtení | Číst metriky |
> | Microsoft.Insights/metricDefinitions/read | Čtení definic metrik |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
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

Umožňuje spravovat zásady související se zabezpečením serverů SQL a databází, ale nikoli k nim přístup.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Network/virtualNetworks/podsítě/joinViaServiceEndpoint/action | Připojí prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozornit. |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Vytvoření a správa zásad auditování serveru SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Vytvoření a správa nastavení auditování serveru SQL |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Načtení podrobností o zásadách auditování objektů blob rozšířeného serveru nakonfigurované na daném serveru |
> | Microsoft.Sql/servery/databáze/auditingZásady/* | Vytvoření a správa zásad auditování databáze serveru SQL server |
> | Microsoft.Sql/servery/databáze/auditingSettings/* | Vytvoření a správa nastavení auditování databáze serveru SQL server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Načtení záznamů auditu objektů blob databáze |
> | Microsoft.Sql/servery/databáze/connectionPolicies/* | Vytvoření a správa zásad připojení k databázi serveru SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servery/databáze/dataMaskingPolicies/* | Vytvoření a správa zásad maskování dat databáze serveru SQL server |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Načtení podrobností o rozšířené zásady auditování objektů blob nakonfigurované v dané databázi |
> | Microsoft.Sql/servery/databáze/čtení | Vrátí seznam databází nebo získá vlastnosti pro zadanou databázi. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servery/databáze/schémata/čtení | Získejte schéma databáze. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Získejte databázový sloupec. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Získejte databázovou tabulku. |
> | Microsoft.Sql/servery/databáze/securityAlertPolicies/* | Vytvoření a správa zásad výstrah zabezpečení databáze serveru SQL server |
> | Microsoft.Sql/servery/databáze/securityMetrics/* | Vytvoření a správa metrik zabezpečení databáze serveru SQL server |
> | Microsoft.Sql/servery/databáze/citlivostLabels/* |  |
> | Microsoft.Sql/servery/databáze/transparentDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servery/čtení | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Vytvoření a správa zásad výstrah serveru SQL server |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
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
        "Microsoft.Support/*"
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

### <a name="sql-server-contributor"></a>Přispěvatel serveru SQL Server

Umožňuje spravovat servery sql a databáze, ale nikoli přístup k nim, a nikoli jejich zásady související se zabezpečením.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servery/* | Vytváření a správa serverů SQL |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Insights/metriky/čtení | Číst metriky |
> | Microsoft.Insights/metricDefinitions/read | Čtení definic metrik |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Úprava zásad auditování serveru SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Úprava nastavení auditování serveru SQL |
> | Microsoft.Sql/servery/databáze/auditingZásady/* | Úprava zásad auditování databáze serveru SQL server |
> | Microsoft.Sql/servery/databáze/auditingSettings/* | Úprava nastavení auditování databáze serveru SQL server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Načtení záznamů auditu objektů blob databáze |
> | Microsoft.Sql/servery/databáze/connectionPolicies/* | Úprava zásad připojení k databázi serveru SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servery/databáze/dataMaskingPolicies/* | Úprava zásad maskování dat databáze serveru SQL server |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servery/databáze/securityAlertPolicies/* | Úprava zásad výstrah zabezpečení databáze serveru SQL server |
> | Microsoft.Sql/servery/databáze/securityMetrics/* | Úprava metrik zabezpečení databáze serveru SQL server |
> | Microsoft.Sql/servery/databáze/citlivostLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Úprava zásad výstrah zabezpečení serveru SQL server |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **Akce data** |  |
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
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
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
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
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

## <a name="analytics"></a>Analýza


### <a name="azure-event-hubs-data-owner"></a>Vlastník dat centra událostí Azure

Umožňuje úplný přístup k prostředkům Centra událostí Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.EventHub/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.EventHub/* |  |
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

### <a name="azure-event-hubs-data-receiver"></a>Přijímač dat centra událostí Azure

Umožňuje přístup k prostředkům Centra událostí Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.EventHub/*/receive/action |  |
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

### <a name="azure-event-hubs-data-sender"></a>Odesílatel dat centra událostí Azure

Umožňuje odesílání přístupu k prostředkům Centra událostí Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.EventHub/*/send/action |  |
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

### <a name="data-factory-contributor"></a>Přispěvatel datové továrny

Vytvářejte a spravujte datové továrny a podřízené prostředky v nich.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.DataFactory/dataTovárny/* | Vytvářejte a spravujte datové továrny a podřízené prostředky v nich. |
> | Microsoft.DataFactory/továrny/* | Vytvářejte a spravujte datové továrny a podřízené prostředky v nich. |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.EventGrid/eventSubscriptions/write | Vytvoření nebo aktualizace odběru událostí |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="data-purger"></a>Vychodění dat

Dokáže vymazat analytická data

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Insights/komponenty/*/čtení |  |
> | Microsoft.Insights/komponenty/vymazání/akce | Vymazání dat z přehledů aplikací |
> | Microsoft.OperationalInsights/pracovní prostory/*/čtení | Zobrazení analytických dat protokolu |
> | Microsoft.OperationalInsights/pracovní prostory/vymazání/akce | Odstranění zadaných dat z pracovního prostoru |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Umožňuje číst a upravovat konfigurace clusteru HDInsight.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.HDInsight/*/čti |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Získání nastavení brány pro cluster HDInsight |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Aktualizace nastavení brány pro cluster HDInsight |
> | Microsoft.HDInsight/clustery/konfigurace/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Resources/deployments/operations/read | Získá nebo zobrazí seznam operací nasazení. |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Může číst, vytvářet, upravovat a odstraňovat operace související s doménovými službami potřebné pro balíček HDInsight Enterprise Security Package

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.AAD/*/čti |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Přispěvatel log Analytics může číst všechna data monitorování a upravovat nastavení monitorování. Úpravy nastavení monitorování zahrnuje přidání rozšíření virtuálních počítače do virtuálních počítače; čtení klíčů účtu úložiště, abyste mohli konfigurovat kolekci protokolů z Azure Storage; vytváření a konfigurace účtů automatizace; přidávání řešení; a konfigurace diagnostiky Azure na všech prostředcích Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | */čtení | Přečtěte si prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Zobrazí seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo přečte diagnostické nastavení pro server Analysis Server. |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Log Analytics Reader můžete zobrazit a prohledat všechna data monitorování, stejně jako a zobrazit nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | */čtení | Přečtěte si prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.OperationalInsights/pracovní prostory/analytika/dotaz/akce | Hledat pomocí nového motoru. |
> | Microsoft.OperationalInsights/pracovní prostory/hledání/akce | Spustí vyhledávací dotaz. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/pracovní prostory/sdílenéKlíče/čtení | Načte sdílené klíče pro pracovní prostor. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | **Akce data** |  |
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

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Přístup k uzlu člena blockchainu (náhled)

Umožňuje přístup k uzlům blockchainových členů

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Blockchain/blockchainČlenové/transakceNodes/číst | Získá nebo zobrazí seznam existujících uzlů transakcí členů blockchainu. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Blockchain/blockchainČlenové/transakceNodes/connect/action | Připojí se k uzlu transakcí člena blockchainu. |
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

## <a name="ai--machine-learning"></a>AI + strojové učení


### <a name="cognitive-services-contributor"></a>Přispěvatel služeb cognitive services

Umožňuje vytvářet, číst, aktualizovat, odstraňovat a spravovat klíče služeb Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Získá funkce předplatného. |
> | Microsoft.Features/providers/features/read | Získá funkci předplatného v daném zprostředkovateli prostředků. |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo přečte diagnostické nastavení pro server Analysis Server. |
> | Microsoft.Insights/logDefinitions/read | Čtení definic protokolů |
> | Microsoft.Insights/metricdefinitions/read | Čtení definic metrik |
> | Microsoft.Insights/metriky/čtení | Číst metriky |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/deployments/operations/read | Získá nebo zobrazí seznam operací nasazení. |
> | Microsoft.Resources/subscriptions/operationresults/read | Získejte výsledky operace předplatného. |
> | Microsoft.Resources/subscriptions/read | Získá seznam odběrů. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="cognitive-services-data-reader-preview"></a>Čtečka dat služeb Cognitive Services (preview)

Umožňuje číst data služeb Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.CognitiveServices/*/čt |  |
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

### <a name="cognitive-services-user"></a>Uživatel kognitivních služeb

Umožňuje číst a seznam klíčů služby Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.CognitiveServices/*/čt |  |
> | Microsoft.CognitiveServices/účty/listkeys/akce | Seznam klíčů |
> | Microsoft.Insights/alertRules/read | Přečtení klasické metriky |
> | Microsoft.Insights/diagnosticSettings/read | Čtení nastavení diagnostiky prostředků |
> | Microsoft.Insights/logDefinitions/read | Čtení definic protokolů |
> | Microsoft.Insights/metricdefinitions/read | Čtení definic metrik |
> | Microsoft.Insights/metriky/čtení | Číst metriky |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/operations/read | Získá nebo zobrazí seznam operací nasazení. |
> | Microsoft.Resources/subscriptions/operationresults/read | Získejte výsledky operace předplatného. |
> | Microsoft.Resources/subscriptions/read | Získá seznam odběrů. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.CognitiveServices/* |  |
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


### <a name="spatial-anchors-account-contributor"></a>Přispěvatel účtu prostorových ukotvení

Umožňuje spravovat prostorové kotvy ve vašem účtu, ale neodstraňovat je

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Vytvoření prostorových kotev |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Objevte blízké prostorové kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Získání vlastností prostorových kotev |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Lokalizovat prostorové kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Odeslání diagnostických dat, která pomohou zlepšit kvalitu služby Azure Spatial Anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aktualizovat vlastnosti prostorových kotev |
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

### <a name="spatial-anchors-account-owner"></a>Vlastník účtu prostorových kotev

Umožňuje spravovat prostorové kotvy ve vašem účtu, včetně jejich odstranění.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Vytvoření prostorových kotev |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Odstranění prostorových kotev |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Objevte blízké prostorové kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Získání vlastností prostorových kotev |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Lokalizovat prostorové kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Odeslání diagnostických dat, která pomohou zlepšit kvalitu služby Azure Spatial Anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aktualizovat vlastnosti prostorových kotev |
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

### <a name="spatial-anchors-account-reader"></a>Čtečka účtů prostorových kotev

Umožňuje vyhledat a číst vlastnosti prostorových kotev ve vašem účtu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Objevte blízké prostorové kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Získání vlastností prostorových kotev |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Lokalizovat prostorové kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Odeslání diagnostických dat, která pomohou zlepšit kvalitu služby Azure Spatial Anchors |
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


### <a name="api-management-service-contributor"></a>Přispěvatel služby správy rozhraní API

Může spravovat služby a api

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ApiManagement/service/* | Vytvoření a správa služby API Management |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="api-management-service-operator-role"></a>Role operátora služby správy rozhraní API

Může spravovat služby, ale ne api

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ApiManagement/service/*/read | Čtení instancí služby API Management Service |
> | Microsoft.ApiManagement/service/backup/action | Služba zálohování rozhraní API pro správu určeného kontejneru v uživatelském účtu úložiště |
> | Microsoft.ApiManagement/service/delete | Odstranit instanci služby správy rozhraní API |
> | Microsoft.ApiManagement/service/managedeployments/action | Změna skladové položky/jednotek, přidání nebo odebrání regionálních nasazení služby API Management Service |
> | Microsoft.ApiManagement/service/read | Čtení metadat pro instanci služby api Management Service |
> | Microsoft.ApiManagement/service/restore/action | Obnovení služby api management u zadaného kontejneru v účtu úložiště za předpokladu uživatele |
> | Microsoft.ApiManagement/service/updatecertificate/action | Nahrání certifikátu TLS/SSL pro službu api Management Service |
> | Microsoft.ApiManagement/service/updatehostname/action | Nastavení, aktualizace nebo odebrání vlastních názvů domén pro službu API Management Service |
> | Microsoft.ApiManagement/service/write | Vytvořit nebo aktualizovat instanci služby api pro správu |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Získání klíčů přidružených k uživateli |
> | **Akce data** |  |
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

### <a name="api-management-service-reader-role"></a>Role čtečky služby API Management Service

Přístup jen pro čtení ke službě a řešením API

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ApiManagement/service/*/read | Čtení instancí služby API Management Service |
> | Microsoft.ApiManagement/service/read | Čtení metadat pro instanci služby api Management Service |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Získání klíčů přidružených k uživateli |
> | **Akce data** |  |
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

### <a name="app-configuration-data-owner"></a>Vlastník konfiguračních dat aplikace

Umožňuje plný přístup k datům konfigurace aplikace.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
> | Microsoft.AppConfiguration/configurationStores/*/write |  |
> | Microsoft.AppConfiguration/configurationStores/*/delete |  |
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

### <a name="app-configuration-data-reader"></a>Čtečka konfiguračních dat aplikace

Umožňuje přístup pro čtení k datům konfigurace aplikace.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | *žádný* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
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

### <a name="azure-service-bus-data-owner"></a>Vlastník dat azure service bus

Umožňuje úplný přístup k prostředkům Azure Service Bus.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.ServiceBus/* |  |
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

### <a name="azure-service-bus-data-receiver"></a>Přijímač dat azure service bus

Umožňuje přístup ke zdrojům Služby Azure Service Bus.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ServiceBus/*/fronty/čtení |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
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

### <a name="azure-service-bus-data-sender"></a>Odesílatel dat azure sběrnice

Umožňuje odesílání přístupu k prostředkům Azure Service Bus.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ServiceBus/*/fronty/čtení |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.ServiceBus/*/send/action |  |
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

### <a name="azure-stack-registration-owner"></a>Vlastník registrace zásobníku Azure

Umožňuje spravovat registrace Azure Stacku.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.AzureStack/registrace/produkty/*/akce |  |
> | Microsoft.AzureStack/registrace/produkty/čtení | Získá vlastnosti produktu Azure Stack Marketplace |
> | Microsoft.AzureStack/registrace/čtení | Získá vlastnosti registrace Azure Stack |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="eventgrid-eventsubscription-contributor"></a>Přispěvatel eventgrideventového předplatného

Umožňuje spravovat operace odběru událostí EventGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypy/eventSubscriptions/read | Seznam odběrů globálních událostí podle typu tématu |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Seznam odběrů místních událostí |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Seznam odběrů místních událostí podle typu tématu |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="eventgrid-eventsubscription-reader"></a>Čtečka eventgrid eventsubscriptionů

Umožňuje číst odběry událostí EventGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.EventGrid/eventSubscriptions/read | Přečtěte si eventSubscription |
> | Microsoft.EventGrid/topicTypy/eventSubscriptions/read | Seznam odběrů globálních událostí podle typu tématu |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Seznam odběrů místních událostí |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Seznam odběrů místních událostí podle typu tématu |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="intelligent-systems-account-contributor"></a>Přispěvatel účtu inteligentních systémů

Umožňuje spravovat účty intelligent systems, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.IntelligentSystems/účty/* | Vytváření a správa inteligentních systémových účtů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Umožňuje spravovat aplikace logiky, ale neměnit přístup k nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Zobrazí seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/read | Vraťte účet úložiště s daným účtem. |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Insights/metricAlerts/* |  |
> | Microsoft.Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo přečte diagnostické nastavení pro server Analysis Server. |
> | Microsoft.Insights/logdefinitions/* | Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. Seznam kategorií protokolu v protokolu aktivit. |
> | Microsoft.Insights/metricDefinitions/* | Čtení definic metrik (seznam dostupných typů metrik pro prostředek). |
> | Microsoft.Logic/* | Spravuje prostředky logic apps. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/operationresults/read | Získejte výsledky operace předplatného. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Web/connectionGateways/* | Vytvořte a spravuje bránu připojení. |
> | Microsoft.Web/připojení/* | Vytvořte a spravuje připojení. |
> | Microsoft.Web/customApis/* | Vytvoří a spravuje vlastní rozhraní API. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Získání vlastností v plánu služby App Service |
> | Microsoft.Web/sites/functions/listSecrets/action | Seznam tajných kódů funkce. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Umožňuje číst, povolit a zakázat aplikace logiky, ale ne upravovat ani aktualizovat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/*/read | Pravidla upozornění na Číst přehledy |
> | Microsoft.Insights/metricAlerts/*/read |  |
> | Microsoft.Insights/diagnosticSettings/*/read | Získá nastavení diagnostiky pro aplikace logiky |
> | Microsoft.Insights/metricDefinitions/*/read | Získá dostupné metriky pro logic apps. |
> | Microsoft.Logic/*/čt | Čte prostředky logic apps. |
> | Microsoft.Logic/workflows/disable/action | Zakáže pracovní postup. |
> | Microsoft.Logic/workflows/enable/action | Povolí pracovní postup. |
> | Microsoft.Logic/workflows/validate/action | Ověří pracovní postup. |
> | Microsoft.Resources/deployments/operations/read | Získá nebo zobrazí seznam operací nasazení. |
> | Microsoft.Resources/subscriptions/operationresults/read | Získejte výsledky operace předplatného. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Web/connectionGateways/*/read | Přečtěte si brány připojení. |
> | Microsoft.Web/connections/*/read | Přečtěte si připojení. |
> | Microsoft.Web/customApis/*/read | Přečtěte si vlastní rozhraní API. |
> | Microsoft.Web/serverFarms/read | Získání vlastností v plánu služby App Service |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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


### <a name="managed-identity-contributor"></a>Přispěvatel spravované identity

Vytvořit, číst, aktualizovat a odstranit přiřazenou identitu uživatele

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Získá existující uživatel přiřazenou identitu |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Vytvoří novou identitu přiřazenou uživateli nebo aktualizuje značky přidružené k existující přiřazené identitě uživatele. |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Odstraní existující identitu přiřazenou uživateli. |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="managed-identity-operator"></a>Operátor spravované identity

Čtení a přiřazení přiřazené identity uživatele

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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


### <a name="azure-sentinel-contributor"></a>Přispěvatel Azure Sentinelu

Přispěvatel Azure Sentinelu

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.SecurityInsights/* |  |
> | Microsoft.OperationalInsights/pracovní prostory/analytika/dotaz/akce | Hledat pomocí nového motoru. |
> | Microsoft.OperationalInsights/pracovní prostory/*/čtení | Zobrazení analytických dat protokolu |
> | Microsoft.OperationalInsights/pracovní prostory/uloženáVyhledávání/* |  |
> | Microsoft.OperationsManagement/solutions/read | Ukončení řešení OMS |
> | Microsoft.OperationalInsights/pracovní prostory/dotaz/čtení | Spouštění dotazů přes data v pracovním prostoru |
> | Microsoft.OperationalInsights/pracovní prostory/dotaz/*/čtení |  |
> | Microsoft.OperationalInsights/pracovní prostory/dataZdroje/čtení | Získejte zdroje dat v pracovním prostoru. |
> | Microsoft.Insights/sešity/* |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="azure-sentinel-reader"></a>Čtečka Azure Sentinel

Čtečka Azure Sentinel

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.SecurityInsights/*/čt |  |
> | Microsoft.OperationalInsights/pracovní prostory/analytika/dotaz/akce | Hledat pomocí nového motoru. |
> | Microsoft.OperationalInsights/pracovní prostory/*/čtení | Zobrazení analytických dat protokolu |
> | Microsoft.OperationalInsights/pracovní prostory/LinkedServices/čt | Získejte propojené služby v daném pracovním prostoru. |
> | Microsoft.OperationalInsights/pracovní prostory/uloženéVyhledávání/čtení | Získá uložený vyhledávací dotaz. |
> | Microsoft.OperationsManagement/solutions/read | Ukončení řešení OMS |
> | Microsoft.OperationalInsights/pracovní prostory/dotaz/čtení | Spouštění dotazů přes data v pracovním prostoru |
> | Microsoft.OperationalInsights/pracovní prostory/dotaz/*/čtení |  |
> | Microsoft.OperationalInsights/pracovní prostory/dataZdroje/čtení | Získejte zdroje dat v pracovním prostoru. |
> | Microsoft.Insights/sešity/čtení | Čtení sešitu |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="azure-sentinel-responder"></a>Azure Sentinel respondér

Azure Sentinel respondér

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.SecurityInsights/*/čt |  |
> | Microsoft.SecurityInsights/cases/* |  |
> | Microsoft.SecurityInsights/incidenty/* |  |
> | Microsoft.OperationalInsights/pracovní prostory/analytika/dotaz/akce | Hledat pomocí nového motoru. |
> | Microsoft.OperationalInsights/pracovní prostory/*/čtení | Zobrazení analytických dat protokolu |
> | Microsoft.OperationalInsights/pracovní prostory/dataZdroje/čtení | Získejte zdroje dat v pracovním prostoru. |
> | Microsoft.OperationalInsights/pracovní prostory/uloženéVyhledávání/čtení | Získá uložený vyhledávací dotaz. |
> | Microsoft.OperationsManagement/solutions/read | Ukončení řešení OMS |
> | Microsoft.OperationalInsights/pracovní prostory/dotaz/čtení | Spouštění dotazů přes data v pracovním prostoru |
> | Microsoft.OperationalInsights/pracovní prostory/dotaz/*/čtení |  |
> | Microsoft.OperationalInsights/pracovní prostory/dataZdroje/čtení | Získejte zdroje dat v pracovním prostoru. |
> | Microsoft.Insights/sešity/čtení | Čtení sešitu |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
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

### <a name="key-vault-contributor"></a>Přispěvatel trezoru klíčů

Umožňuje spravovat trezory klíčů, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Vymazání obnovitelného odstraněného trezoru klíčů |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **Akce data** |  |
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
        "Microsoft.KeyVault/hsmPools/*"
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

### <a name="security-admin"></a>Správce zabezpečení

Umí zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení, zavřít výstrahy a doporučení.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Authorization/policyAssignments/* | Vytváření a správa přiřazení zásad |
> | Microsoft.Authorization/policyDefinitions/* | Vytváření a správa definic zásad |
> | Microsoft.Authorization/policySetDefinitions/* | Vytváření a správa sad zásad |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | Microsoft.operationalInsights/workspaces/*/read | Zobrazení analytických dat protokolu |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Security/* | Vytváření a správa součástí a zásad zabezpečení |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="security-assessment-contributor"></a>Přispěvatel hodnocení zabezpečení

Umožňuje nabízení hodnocení do centra zabezpečení.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Security/assessments/write | Vytvoření nebo aktualizace hodnocení zabezpečení předplatného |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Toto je starší role. Místo toho použijte správce zabezpečení.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.ClassicCompute/*/read | Čtení informací o konfiguraci klasické virtuální počítače |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Konfigurace zápisu pro klasické virtuální počítače |
> | Microsoft.ClassicNetwork/*/čti | Přečtěte si informace o konfiguraci klasické sítě |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Security/* | Vytváření a správa součástí a zásad zabezpečení |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="security-reader"></a>Čtečka zabezpečení

Umí zobrazit doporučení a výstrahy, zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, ale nemůže provádět změny.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.operationalInsights/workspaces/*/read | Zobrazení analytických dat protokolu |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Security/*/čt | Čtení součástí a zásad zabezpečení |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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
        "Microsoft.Insights/alertRules/*",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*",
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


### <a name="devtest-labs-user"></a>Uživatel devTest Labs

Umožňuje připojit, spustit, restartovat a vypnout virtuální počítače ve svých laboratořích Azure DevTest.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Compute/availabilitySets/read | Získání vlastností sady dostupnosti |
> | Microsoft.Compute/virtualMachines/*/read | Čtení vlastností virtuálního počítače (velikosti virtuálních počítačů, stav za běhu, rozšíření virtuálních počítačů atd.) |
> | Microsoft.Compute/virtualMachines/navrátit/akce | Vypne virtuální počítač a uvolní výpočetní prostředky |
> | Microsoft.Compute/virtualMachines/read | Získání vlastností virtuálního počítače |
> | Microsoft.Compute/virtualMachines/restart/action | Restartuje virtuální počítač. |
> | Microsoft.Compute/virtualMachines/start/action | Spustí virtuální počítač. |
> | Microsoft.DevTestLab/*/čti | Přečtěte si vlastnosti laboratoře |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Nárokovat náhodné nárokovatelný virtuální počítač v laboratoři. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Vytvořte virtuální počítače v testovacím prostředí. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Ujistěte se, že aktuální uživatel má platný profil v testovacím prostředí. |
> | Microsoft.DevTestLab/labs/formulas/delete | Odstranit vzorce. |
> | Microsoft.DevTestLab/labs/formulas/read | Přečtěte si vzorce. |
> | Microsoft.DevTestLab/labs/formulas/write | Přidejte nebo upravte vzorce. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Vyhodnotí zásady testovacího prostředí. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Převzetí vlastnictví existujícího virtuálního počítače |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action Microsoft. | Seznam příslušných plánů zahájení/zastavení, pokud existuje. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Získá řetězec, který představuje obsah souboru RDP pro virtuální počítač |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Připojí se k fondu back-endových adres vykladače vyrovnávání zatížení. Nelze upozornit. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Připojí se k pravidlu příchozího nat ovače zatížení. Nelze upozornit. |
> | Microsoft.Network/networkInterfaces/*/read | Přečtěte si vlastnosti síťového rozhraní (například všechny vykladače zatížení, kterých je síťové rozhraní součástí) |
> | Microsoft.Network/networkInterfaces/join/action | Připojí virtuální počítač k síťovému rozhraní. Nelze upozornit. |
> | Microsoft.Network/networkInterfaces/read | Získá definici síťového rozhraní.  |
> | Microsoft.Network/networkInterfaces/write | Vytvoří síťové rozhraní nebo aktualizuje existující síťové rozhraní.  |
> | Microsoft.Network/publicIPAdresy/*/čti | Čtení vlastností veřejné IP adresy |
> | Microsoft.Network/publicIPAdresy/připojit/akce | Připojí se k veřejné IP adrese. Nelze upozornit. |
> | Microsoft.Network/publicIPAdresy/čtení | Získá definici veřejné IP adresy. |
> | Microsoft.Network/virtualNetworks/podsítě/join/action | Připojí se k virtuální síti. Nelze upozornit. |
> | Microsoft.Resources/deployments/operations/read | Získá nebo zobrazí seznam operací nasazení. |
> | Microsoft.Resources/deployments/read | Získá nebo zobrazí seznam nasazení. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Seznamy dostupných velikostí, na které lze virtuální počítač aktualizovat |
> | **Akce data** |  |
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

### <a name="lab-creator"></a>Lab Tvůrce

Umožňuje vytvářet, spravovat a odstraňovat spravované laboratoře v rámci účtů Azure Lab.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Vytvořte testovací prostředí v účtu testovacího prostředí. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Získání místních informací o dostupnosti pro každou kategorii velikostí nakonfigurovaných v rámci účtu testovacího prostředí |
> | Microsoft.LabServices/labAccounts/getPricingAndAvailability/action | Získejte ceny a dostupnost kombinací velikostí, zeměpisných oblastí a operačních systémů pro účet testovacího prostředí. |
> | Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action | Získejte základní omezení a využití tohoto předplatného |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | *žádný* |  |
> | **NotDataActions** |  |
> | *žádný* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, manage, delete your managed labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getRegionalAvailability/action",
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


### <a name="application-insights-component-contributor"></a>Přispěvatel komponenty Přehledy aplikací

Umožňuje spravovat komponenty Application Insights

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa klasických pravidel výstrah |
> | Microsoft.Insights/metricAlerts/* | Vytvoření a správa nových pravidel výstrah |
> | Microsoft.Insights/komponenty/* | Vytváření a správa komponent Přehledů |
> | Microsoft.Insights/webtests/* | Vytváření a správa webových testů Přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="application-insights-snapshot-debugger"></a>Ladicí program snímku přehledů aplikací

Poskytuje uživateli oprávnění k zobrazení a stažení ladicích snímků shromážděných pomocí ladicího programu snímek Fonýře přehledů aplikací. Všimněte si, že tato oprávnění nejsou zahrnuty v rolích [vlastníka](#owner) nebo [přispěvatele.](#contributor)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Insights/komponenty/*/čtení |  |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Dokáže číst všechna data monitorování a upravovat nastavení monitorování. Viz taky [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | */čtení | Přečtěte si prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/skupiny akcí/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Insights/komponenty/* | Vytváření a správa komponent Přehledů |
> | Microsoft.Insights/DiagnosticSettings/* | Vytvoří, aktualizuje nebo přečte diagnostické nastavení pro server Analysis Server. |
> | Microsoft.Insights/eventtypes/* | Seznam událostí protokolu aktivit (události správy) v předplatném. Toto oprávnění se vztahuje na programový i portálový přístup k protokolu aktivit. |
> | Microsoft.Insights/LogDefinitions/* | Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. Seznam kategorií protokolu v protokolu aktivit. |
> | Microsoft.Insights/upozornění na metriky/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Čtení definic metrik (seznam dostupných typů metrik pro prostředek). |
> | Microsoft.Insights/Metriky/* | Přečtěte si metriky pro prostředek. |
> | Microsoft.Insights/Register/Action | Registrace poskytovatele Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Vytváření a správa webových testů Přehledů |
> | Microsoft.Insights/sešity/* |  |
> | Microsoft.OperationalInsights/pracovní prostory/zpravodajské balíčky/* | Balíčky řešení log analytics pro čtení/zápis/odstranění. |
> | Microsoft.OperationalInsights/pracovní prostory/uloženáVyhledávání/* | Čtení / zápis / odstranění log analytics uložená vyhledávání. |
> | Microsoft.OperationalInsights/pracovní prostory/hledání/akce | Spustí vyhledávací dotaz. |
> | Microsoft.OperationalInsights/pracovní prostory/sdílenéKlíče/akce | Načte sdílené klíče pro pracovní prostor. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Microsoft.OperationalInsights/pracovní prostory/storageinsightconfigs/* | Konfigurace přehledu úložiště analýzy protokolů pro čtení a zápis/zápis. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.WorkloadMonitor/monitory/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/* |  |
> | Microsoft.AlertsManagement/actionRules/* |  |
> | Microsoft.AlertsManagement/smartGroups/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="monitoring-metrics-publisher"></a>Vydavatel metrik monitorování

Umožňuje metriky publikování oproti prostředkům Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Insights/Register/Action | Registrace poskytovatele Microsoft Insights |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.Insights/Metriky/Zápis | Metriky zápisu |
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

### <a name="monitoring-reader"></a>Monitorovací čtečka

Umí číst všechna data monitorování (metriky, protokoly atd.). Viz taky [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | */čtení | Přečtěte si prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.OperationalInsights/pracovní prostory/hledání/akce | Spustí vyhledávací dotaz. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Sdílené sešity lze uložit.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Insights/sešity/zápis | Vytvoření nebo aktualizace sešitu |
> | Microsoft.Insights/sešity/odstranit | Odstranění sešitu |
> | Microsoft.Insights/sešity/čtení | Čtení sešitu |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="workbook-reader"></a>Čtečka sešitů

Umí číst sešity.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | microsoft.insights/sešity/čtení | Čtení sešitu |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

## <a name="management--governance"></a>Management + řízení


### <a name="automation-job-operator"></a>Automatizace Obsluha úloh

Vytvářejte a spravujte úlohy pomocí runbooků automatizace.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/čt | Přečte hybridní prostředky pracovníků runbooku |
> | Microsoft.Automation/automationAccounts/jobs/read | Získá úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Obnoví úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Zastaví úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Získá datový proud úloh azure automatizace |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pozastaví úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Vytvoří úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Získá výstup úlohy |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Automatizační operátoři mohou zahajovat, zastavovat, pozastavovat a pokračovat v práci

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/čt | Přečte hybridní prostředky pracovníků runbooku |
> | Microsoft.Automation/automationAccounts/jobs/read | Získá úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Obnoví úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Zastaví úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Získá datový proud úloh azure automatizace |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pozastaví úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Vytvoří úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Získá plán úloh Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Vytvoří plán úloh Azure Automation |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Získá pracovní prostor propojený s účtem automatizace |
> | Microsoft.Automation/automationAccounts/read | Získá účet Azure Automation |
> | Microsoft.Automation/automationAccounts/runbooks/read | Získá runbook Azure Automation |
> | Microsoft.Automation/automationAccounts/plány/čtení | Získá prostředek plánu Azure Automation |
> | Microsoft.Automation/automationAccounts/schedules/write | Vytvoří nebo aktualizuje datový zdroj plánu Azure Automation |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Získá výstup úlohy |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="automation-runbook-operator"></a>Automatizace Runbook Operátor

Přečtěte si vlastnosti runbooku – abyste mohli vytvářet úlohy runbooku.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Automation/automationAccounts/runbooks/read | Získá runbook Azure Automation |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="azure-connected-machine-onboarding"></a>Připojení připojeného počítače Azure

Můžete na palubě připojených počítačů Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.HybridCompute/machines/read | Přečtěte si všechny počítače Azure Arc |
> | Microsoft.HybridCompute/machines/write | Zapíše počítače Azure Arc |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Získejte přiřazení konfigurace hosta. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="azure-connected-machine-resource-administrator"></a>Správce prostředků připojeného počítače Azure

Můžete číst, zapisovat, odstraňovat a znovu nastoupit na Azure Connected Machines.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.HybridCompute/machines/read | Přečtěte si všechny počítače Azure Arc |
> | Microsoft.HybridCompute/machines/write | Zapíše počítače Azure Arc |
> | Microsoft.HybridCompute/machines/delete | Odstraní počítače Azure Arc |
> | Microsoft.HybridCompute/machines/reconnect/action | Znovu připojí počítače Azure Arc |
> | Microsoft.HybridCompute/machines/extensions/write | Nainstaluje nebo aktualizuje rozšíření Azure Arc |
> | Microsoft.HybridCompute/*/read |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Umožňuje přístup pro čtení k fakturačním údajům

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Billing/*/čt | Číst fakturační údaje |
> | Microsoft.Commerce/*/čti |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | Microsoft.CostManagement/*/čti |  |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Můžete spravovat definice podrobného plánu, ale ne přiřazovat je.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Blueprint/blueprints/* | Vytvářejte a spravujte definice podrobného plánu nebo artefakty podrobného plánu. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="blueprint-operator"></a>Operátor plánu

Můžete přiřadit existující publikované podrobné plány, ale nelze vytvořit nové podrobné plány. Všimněte si, že to funguje pouze v případě, že přiřazení se provádí s uživatelem přiřazenou spravovanou identitou.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Blueprint/blueprintÚkoly/* | Vytvářejte a spravujte přiřazení podrobného plánu. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="cost-management-contributor"></a>Přispěvatel pro řízení nákladů

Dokáže zobrazit náklady a spravovat konfiguraci nákladů (např. rozpočty, exporty)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Získá seznam odběrů. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Advisor/configurations/read | Získat konfigurace |
> | Microsoft.Advisor/recommendations/read | Čte doporučení |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="cost-management-reader"></a>Čtečka správy nákladů

Může zobrazit údaje o nákladech a konfiguraci (např. rozpočty, exporty)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/čti |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Získá seznam odběrů. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Advisor/configurations/read | Získat konfigurace |
> | Microsoft.Advisor/recommendations/read | Čte doporučení |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="managed-application-contributor-role"></a>Role přispěvatele spravované aplikace

Umožňuje vytváření prostředků spravovaných aplikací.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | */čtení | Přečtěte si prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.Solutions/applications/* |  |
> | Microsoft.Solutions/registr/akce | Zaregistrujte se do řešení. |
> | Microsoft.Resources/subscriptions/resourceGroups/* |  |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="managed-application-operator-role"></a>Role operátoru spravované aplikace

Umožňuje číst a provádět akce s prostředky spravovaných aplikací.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | */čtení | Přečtěte si prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.Solutions/applications/read | Načte seznam aplikací. |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="managed-applications-reader"></a>Čtečka spravovaných aplikací

Umožňuje číst prostředky ve spravované aplikaci a požádat o přístup k JIT.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | */čtení | Přečtěte si prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="managed-services-registration-assignment-delete-role"></a>Přiřazení registrace spravovaných služeb Odstranit roli

Role odstranění registrace spravovaných služeb umožňuje správě uživatelů klienta odstranit přiřazení registrace přiřazené jejich tenantovi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.ManagedServices/registrationAssignments/read | Načte seznam přiřazení registrace spravovaných služeb. |
> | Microsoft.ManagedServices/registrationAssignments/delete | Odebere přiřazení registrace spravovaných služeb. |
> | Microsoft.ManagedServices/operationStaves/read | Přečte stav operace pro prostředek. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

Role přispěvatele skupiny pro správu

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Management/managementGroups/delete | Odstranit skupinu pro správu. |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Zrušení přidružení předplatného ze skupiny pro správu. |
> | Microsoft.Management/managementGroups/subscriptions/write | Přidruží existující předplatné ke skupině pro správu. |
> | Microsoft.Management/managementGroups/write | Vytvořte nebo aktualizujte skupinu pro správu. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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
        "Microsoft.Management/managementGroups/write"
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

### <a name="management-group-reader"></a>Čtečka skupin pro správu

Role čtečky skupiny pro správu

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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
        "Microsoft.Management/managementGroups/read"
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

### <a name="new-relic-apm-account-contributor"></a>Nový přispěvatel účtu Relic APM

Umožňuje spravovat nové účty a aplikace správy výkonu aplikací, ale nikoli k nim přístup.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | NewRelic.APM/účty/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="policy-insights-data-writer-preview"></a>Zapisovač dat přehledů zásad (náhled)

Umožňuje přístup pro čtení k zásadám prostředků a zápis u událostí zásad součástí prostředků.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/policyassignments/read | Získejte informace o přiřazení zásad. |
> | Microsoft.Authorization/policydefinitions/read | Získejte informace o definici zásady. |
> | Microsoft.Authorization/policysetdefinitions/read | Získejte informace o definici sady zásad. |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
> | Microsoft.PolicyInsights/checkDataPolicyCompliance/action | Zkontrolujte stav dodržování předpisů dané součásti podle zásad dat. |
> | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | Protokolovat události zásad součásti prostředků. |
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

Uživatelé s právy k vytváření nebo úpravám zásad prostředků, vytváření lístku podpory a čtení prostředků/hierarchie.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | */čtení | Přečtěte si prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.Authorization/policyassignments/* | Vytváření a správa přiřazení zásad |
> | Microsoft.Authorization/policydefinitions/* | Vytváření a správa definic zásad |
> | Microsoft.Authorization/policysetdefinitions/* | Vytváření a správa sad zásad |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="site-recovery-contributor"></a>Přispěvatel obnovení webu

Umožňuje spravovat službu Site Recovery s výjimkou vytváření trezoru a přiřazení rolí.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Network/virtualNetworks/read | Získání definice virtuální sítě |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace používaná službou |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp je interní operace používaná službou |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operace Aktualizovat certifikát prostředku aktualizuje certifikát pověření prostředku nebo trezoru. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Vytváření a správa rozšířených informací týkajících se trezoru |
> | Microsoft.RecoveryServices/Vaults/read | Operace Získat úložiště získá objekt představující prostředek Azure typu trezor |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Vytváření a správa registrovaných identit |
> | Microsoft.RecoveryServices/trezory/replicationAlertSettings/* | Vytvořit nebo aktualizovat nastavení výstrah replikace |
> | Microsoft.RecoveryServices/trezory/replicationEvents/read | Přečtěte si všechny události |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/* | Vytváření a správa prostředků infrastruktury replikace |
> | Microsoft.RecoveryServices/trezory/replikaceÚlohy/* | Vytváření a správa úloh replikace |
> | Microsoft.RecoveryServices/trezory/replikaceZásady/* | Vytvoření a správa zásad replikace |
> | Microsoft.RecoveryServices/trezory/replicationRecoveryPlans/* | Vytváření a správa plánů obnovení |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Vytvoření a správa konfigurace úložiště trezoru služby Recovery Services |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti o využití úložiště služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operaci tokenu úložiště lze použít k získání tokenu úložiště pro operace back-endu na úrovni trezoru. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Čtení upozornění pro trezor služeb obnovení |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfigurace/oznámeníKonfigurace/čtení |  |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft.RecoveryServices/trezory/replicationOperationStatus/read | Čtení stavu operace replikace úložiště |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="site-recovery-operator"></a>Operátor obnovení webu

Umožňuje převzetí služeb při selhání a navrácení služeb po obnovení služeb při selhání, ale neprovádět jiné operace správy obnovení webu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Network/virtualNetworks/read | Získání definice virtuální sítě |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace používaná službou |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp je interní operace používaná službou |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu představující prostředek Azure typu ?trezor? |
> | Microsoft.RecoveryServices/Vaults/read | Operace Získat úložiště získá objekt představující prostředek Azure typu trezor |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operace Získat výsledky operace lze použít získat stav operace a výsledek pro asynchronně odeslanou operaci. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnery operace lze získat kontejnery registrované pro prostředek. |
> | Microsoft.RecoveryServices/trezory/replicationAlertSettings/read | Přečtěte si nastavení výstrah |
> | Microsoft.RecoveryServices/trezory/replicationEvents/read | Přečtěte si všechny události |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/checkKonzistence/akce | Kontroluje konzistenci tkaniny |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/read | Přečtěte si všechny látky |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/reassociateGateway/action | Znovu přidružit bránu |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/renewcertificate/action | Obnovit certifikát pro fabric |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/replicationNetworks/read | Přečtěte si libovolné sítě |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Přečtěte si všechna mapování sítě |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/read | Přečtěte si všechny ochranné kontejnery |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Přečtěte si všechny chránitelné položky |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Použít bod obnovení |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Potvrzení převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Plánované převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Čtení všech chráněných položek |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Čtení všech bodů obnovení replikace |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Oprava replikace |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Znovu chránit chráněnou položku |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Kontejner ochrany spínače |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testovací převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Otestovat vyčištění převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizovat službu mobility |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Přečtěte si mapování kontejnerů ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Přečtěte si všechny poskytovatele služeb obnovení |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Aktualizovat zprostředkovatele |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/read | Přečtěte si všechny klasifikace úložiště |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Přečtěte si všechna mapování klasifikace úložiště |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/replicationvCenters/read | Přečtěte si všechny vCenters |
> | Microsoft.RecoveryServices/trezory/replikaceÚlohy/* | Vytváření a správa úloh replikace |
> | Microsoft.RecoveryServices/trezory/replikaceZásady/čtení | Přečtěte si všechny zásady |
> | Microsoft.RecoveryServices/trezory/replikaceRecoveryPlans/failoverCommit/action | Plán obnovení potvrzení o převzetí služeb při selhání |
> | Microsoft.RecoveryServices/trezory/replicationRecoveryPlans/plannedFailover/action | Plánovaný plán obnovení převzetí služeb při selhání |
> | Microsoft.RecoveryServices/trezory/replicationRecoveryPlans/read | Přečtěte si všechny plány obnovy |
> | Microsoft.RecoveryServices/trezory/replicationRecoveryPlans/reProtect/action | Znovu chránit plán obnovení |
> | Microsoft.RecoveryServices/trezory/replicationRecoveryPlans/testFailover/action | Otestovat plán obnovení převzetí služeb při selhání |
> | Microsoft.RecoveryServices/trezory/replicationRecoveryPlans/testFailoverCleanup/action | Otestovat plán obnovení obnovení s podporou převzetí služeb při selhání |
> | Microsoft.RecoveryServices/trezory/replicationRecoveryPlans/unplannedFailover/action | Plán obnovení převzetí služeb při selhání |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Čtení upozornění pro trezor služeb obnovení |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfigurace/oznámeníKonfigurace/čtení |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti o využití úložiště služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operaci tokenu úložiště lze použít k získání tokenu úložiště pro operace back-endu na úrovni trezoru. |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="site-recovery-reader"></a>Čtečka obnovení webu

Umožňuje zobrazit stav Obnovení webu, ale neprovádět jiné operace správy.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace používaná službou |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu představující prostředek Azure typu ?trezor? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb obnovení. |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfigurace/oznámeníKonfigurace/čtení |  |
> | Microsoft.RecoveryServices/Vaults/read | Operace Získat úložiště získá objekt představující prostředek Azure typu trezor |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operace Získat výsledky operace lze použít získat stav operace a výsledek pro asynchronně odeslanou operaci. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnery operace lze získat kontejnery registrované pro prostředek. |
> | Microsoft.RecoveryServices/trezory/replicationAlertSettings/read | Přečtěte si nastavení výstrah |
> | Microsoft.RecoveryServices/trezory/replicationEvents/read | Přečtěte si všechny události |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/read | Přečtěte si všechny látky |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/replicationNetworks/read | Přečtěte si libovolné sítě |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Přečtěte si všechna mapování sítě |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/read | Přečtěte si všechny ochranné kontejnery |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Přečtěte si všechny chránitelné položky |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Čtení všech chráněných položek |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Čtení všech bodů obnovení replikace |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Přečtěte si mapování kontejnerů ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Přečtěte si všechny poskytovatele služeb obnovení |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/read | Přečtěte si všechny klasifikace úložiště |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Přečtěte si všechna mapování klasifikace úložiště |
> | Microsoft.RecoveryServices/trezory/replicationFabrics/replicationvCenters/read | Přečtěte si všechny vCenters |
> | Microsoft.RecoveryServices/trezory/replicationJobs/read | Přečtěte si všechny úlohy |
> | Microsoft.RecoveryServices/trezory/replikaceZásady/čtení | Přečtěte si všechny zásady |
> | Microsoft.RecoveryServices/trezory/replicationRecoveryPlans/read | Přečtěte si všechny plány obnovy |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti o využití úložiště služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operaci tokenu úložiště lze použít k získání tokenu úložiště pro operace back-endu na úrovni trezoru. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="support-request-contributor"></a>Přispěvatel žádosti o podporu

Umožňuje vytvářet a spravovat žádosti o podporu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="tag-contributor"></a>Přispěvatel značky

Umožňuje spravovat značky na entitách, aniž by poskytoval přístup k entitám samotným.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Získá prostředky pro skupinu prostředků. |
> | Microsoft.Resources/subscriptions/resources/read | Získá prostředky předplatného. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | Microsoft.Resources/tags/* |  |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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


### <a name="biztalk-contributor"></a>BizTalk přispěvatel

Umožňuje spravovat BizTalk služby, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.BizTalkServices/BizTalk/* | Vytváření a správa BizTalk služeb |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

### <a name="scheduler-job-collections-contributor"></a>Přispěvatel kolekcí úloh plánovače

Umožňuje spravovat kolekce úloh plánovače, ale ne přístup k nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytváření a správa pravidel výstrah přehledů |
> | Microsoft.ResourceHealth/availabilityStaves/čtení | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft.Resources/deployments/* | Vytvoření a správa nasazení skupin prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Získá nebo zobrazí seznam skupin prostředků. |
> | Microsoft.Scheduler/jobcollections/* | Vytváření a správa kolekcí úloh |
> | Microsoft.Support/* | Vytvoření a správa lístků podpory |
> | **NotActions** |  |
> | *žádný* |  |
> | **Akce data** |  |
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

- [Shoda s poskytovatelem prostředků se službou](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Vlastní role Azure](custom-roles.md)
- [Oprávnění ve službě Azure Security Center](../security-center/security-center-permissions.md)
