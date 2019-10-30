---
title: Předdefinované role pro prostředky Azure | Microsoft Docs
description: Popisuje předdefinované role pro řízení přístupu na základě role (RBAC) a prostředky Azure. Zobrazí seznam akcí, NotActions, dataactions a NotDataActions.
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
ms.date: 10/28/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: dff222788480eea882614ad29478df1dce359199
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73101292"
---
# <a name="built-in-roles-for-azure-resources"></a>Předdefinované role pro prostředky Azure

[Řízení přístupu na základě role (RBAC)](overview.md) má několik předdefinovaných rolí pro prostředky Azure, které můžete přiřadit uživatelům, skupinám, objektům služby a spravovaným identitám. Přiřazení rolí představují způsob, jakým ovládáte přístup k prostředkům Azure. Pokud předdefinované role nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní [role pro prostředky Azure](custom-roles.md).

V tomto článku jsou uvedené předdefinované role pro prostředky Azure, které se vždycky vyvíjejí. K získání nejnovějších rolí použijte příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) nebo [AZ role definition list](/cli/azure/role/definition#az-role-definition-list). Pokud hledáte role správce pro Azure Active Directory, přečtěte si téma [oprávnění role správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Předdefinované popisy rolí

Následující tabulka uvádí stručný popis každé předdefinované role. Kliknutím na název role zobrazíte seznam `Actions`, `NotActions`, `DataActions`a `NotDataActions` pro každou roli. Informace o tom, co tyto akce znamenají a jak se vztahují na datové roviny pro správu a data, najdete v tématu [vysvětlení definic rolí pro prostředky Azure](role-definitions.md).


| Předdefinovaná role | Popis |
| --- | --- |
| [Vlastník](#owner) | Umožňuje správu všeho, včetně přístupu k prostředkům. |
| [Přispěvatel](#contributor) | Umožňuje spravovat vše kromě udělení přístupu k prostředkům. |
| [Čtenář](#reader) | Umožňuje zobrazit vše, ale ne provádět žádné změny. |
| [AcrDelete](#acrdelete) | Odstranit ACR |
| [AcrImageSigner](#acrimagesigner) | podpis ACR image |
| [AcrPull](#acrpull) | ACR pull |
| [AcrPush](#acrpush) | nabízení ACR |
| [AcrQuarantineReader](#acrquarantinereader) | čtečka dat karantény ACR |
| [AcrQuarantineWriter](#acrquarantinewriter) | zapisovač dat karantény ACR |
| [Přispěvatel API Management služby](#api-management-service-contributor) | Může spravovat službu a rozhraní API. |
| [Role operátora služby API Management](#api-management-service-operator-role) | Může spravovat službu, ale ne rozhraní API. |
| [Role čtecího modulu služby API Management](#api-management-service-reader-role) | Přístup ke službě a rozhraním API jen pro čtení |
| [Přispěvatel Application Insights komponent](#application-insights-component-contributor) | Může spravovat součásti Application Insights. |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Poskytuje uživateli oprávnění k zobrazení a stažení snímků ladění shromážděných pomocí Snapshot Debugger Application Insights. Všimněte si, že tato oprávnění nejsou zahrnutá v rolích [vlastník](#owner) nebo [Přispěvatel](#contributor) . |
| [Operátor úlohy služby Automation](#automation-job-operator) | Vytvářejte a spravujte úlohy pomocí runbooků Automation. |
| [Operátor automatizace](#automation-operator) | Operátory automatizace můžou spouštět, zastavovat, pozastavovat a obnovovat úlohy. |
| [Operátor Runbooku služby Automation](#automation-runbook-operator) | Číst vlastnosti Runbooku – abyste mohli vytvářet úlohy Runbooku. |
| [Přispěvatel avere](#avere-contributor) | Může vytvořit a spravovat cluster avere vFXT. |
| [Avere – operátor](#avere-operator) | Používá cluster avere vFXT ke správě clusteru. |
| [Vlastník dat Event Hubs Azure](#azure-event-hubs-data-owner) | Umožňuje úplný přístup k prostředkům Azure Event Hubs. |
| [Přijímač dat Event Hubs Azure](#azure-event-hubs-data-receiver) | Umožňuje získat přístup k prostředkům Azure Event Hubs. |
| [Odesilatel dat Event Hubs Azure](#azure-event-hubs-data-sender) | Povoluje odesílání přístup k prostředkům Azure Event Hubs. |
| [Role Správce clusteru služby Azure Kubernetes](#azure-kubernetes-service-cluster-admin-role) | Vypíše akci přihlašovacích údajů správce clusteru. |
| [Role uživatele clusteru služby Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Vypíše akci přihlašovacích údajů uživatele clusteru. |
| [Čtečka dat Azure Maps (Preview)](#azure-maps-data-reader-preview) | Udělí přístup ke čtení dat souvisejících s mapou z účtu Azure Maps. |
| [Přispěvatel Sentinel Azure](#azure-sentinel-contributor) | Přispěvatel Sentinel Azure |
| [Čtečka Sentinel Azure](#azure-sentinel-reader) | Čtečka Sentinel Azure |
| [Respondér služby Azure Sentinel](#azure-sentinel-responder) | Respondér služby Azure Sentinel |
| [Azure Service Bus vlastník dat](#azure-service-bus-data-owner) | Umožňuje úplný přístup k Azure Service Bus prostředkům. |
| [Azure Service Bus přijímač dat](#azure-service-bus-data-receiver) | Umožňuje získat přístup k prostředkům Azure Service Bus. |
| [Azure Service Bus odesílatel dat](#azure-service-bus-data-sender) | Umožňuje odeslat přístup k prostředkům Azure Service Bus. |
| [Vlastník registrace Azure Stack](#azure-stack-registration-owner) | Umožňuje spravovat Azure Stack registrace. |
| [Přispěvatel zálohování](#backup-contributor) | Umožňuje správu služby zálohování, ale nemůže vytvářet trezory a udělovat přístup jiným uživatelům. |
| [Operátor zálohování](#backup-operator) | Umožňuje správu zálohovacích služeb s výjimkou odebrání zálohování, vytváření trezoru a poskytování přístupu jiným uživatelům. |
| [Čtečka zálohování](#backup-reader) | Může zobrazovat služby zálohování, ale nemůže provádět změny. |
| [Čtenář fakturace](#billing-reader) | Povolí přístup pro čtení k fakturačním údajům. |
| [Přispěvatel BizTalk](#biztalk-contributor) | Umožňuje spravovat BizTalk Services, ale ne přístup k nim. |
| [Přístup ke členskému uzlu blockchain (Preview)](#blockchain-member-node-access-preview) | Umožňuje přístup k blockchain členským uzlům. |
| [Přispěvatel podrobného plánu](#blueprint-contributor) | Může spravovat definice podrobného plánu, ale nepřiřazovat je. |
| [Operátor podrobného plánu](#blueprint-operator) | Může přiřadit existující publikované modrotisky, ale nemůže vytvářet nové plány. Poznámka: Tato akce funguje pouze v případě, že je přiřazení provedeno pomocí uživatelem přiřazené spravované identity. |
| [Přispěvatel koncového bodu CDN](#cdn-endpoint-contributor) | Může spravovat koncové body CDN, ale nemůže udělit přístup jiným uživatelům. |
| [Čtečka koncového bodu CDN](#cdn-endpoint-reader) | Může zobrazovat koncové body CDN, ale nemůže provádět změny. |
| [Přispěvatel profilu CDN](#cdn-profile-contributor) | Může spravovat profily CDN a jejich koncové body, ale nemůže udělit přístup jiným uživatelům. |
| [Čtečka profilů CDN](#cdn-profile-reader) | Může zobrazit profily CDN a jejich koncové body, ale nemůže provádět změny. |
| [Přispěvatel klasických sítí](#classic-network-contributor) | Umožňuje správu klasických sítí, ale ne přístup k nim. |
| [Přispěvatel klasických účtů úložiště](#classic-storage-account-contributor) | Umožňuje správu klasických účtů úložiště, ale ne přístup k nim. |
| [Role služby operátora klíče klasického účtu úložiště](#classic-storage-account-key-operator-service-role) | Operátoři klíčů pro klasický účet úložiště můžou vypisovat a znovu generovat klíče na klasických účtech úložiště. |
| [Přispěvatel klasických virtuálních počítačů](#classic-virtual-machine-contributor) | Umožňuje správu klasických virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým se připojuje. |
| [Přispěvatel Cognitive Services](#cognitive-services-contributor) | Umožňuje vytvářet, číst, aktualizovat, odstraňovat a spravovat klíče Cognitive Services. |
| [Čtečka dat Cognitive Services (Preview)](#cognitive-services-data-reader-preview) | Umožňuje číst Cognitive Services data. |
| [Cognitive Services uživatel](#cognitive-services-user) | Umožňuje číst a zobrazit seznam klíčů Cognitive Services. |
| [Role čtečky účtu Cosmos DB](#cosmos-db-account-reader-role) | Může číst data Azure Cosmos DB účtu. Správa účtů Azure Cosmos DB najdete v tématu [Přispěvatel účtu DocumentDB](#documentdb-account-contributor) . |
| [Operátor Cosmos DB](#cosmos-db-operator) | Umožňuje správu účtů Azure Cosmos DB, ale ne přístup k datům v nich. Zabraňuje přístup k klíčům účtu a připojovacím řetězcům. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Může odeslat žádost o obnovení pro Cosmos DB databázi nebo kontejner pro účet. |
| [Přispěvatel Cost Management](#cost-management-contributor) | Umožňuje zobrazit náklady a spravovat konfiguraci nákladů (např. rozpočty, exporty). |
| [Čtecí modul Cost Management](#cost-management-reader) | Může zobrazit data a konfiguraci nákladů (např. rozpočty, exporty). |
| [Přispěvatel Data Box](#data-box-contributor) | Umožňuje spravovat všechno, co služba Data Box, s výjimkou poskytnutí přístupu jiným uživatelům. |
| [Čtecí modul Data Box](#data-box-reader) | Umožňuje správu Data Box služby s výjimkou vytváření pořadí nebo úprav podrobností objednávky a udělení přístupu jiným uživatelům. |
| [Přispěvatel Data Factory](#data-factory-contributor) | Vytváření a Správa datových továren a také podřízených prostředků v nich. |
| [Vývojář Data Lake Analytics](#data-lake-analytics-developer) | Umožňuje odesílat, monitorovat a spravovat vlastní úlohy, ale neumožňuje vytvářet ani odstraňovat Data Lake Analytics účty. |
| [Nástroj pro vyprázdnění dat](#data-purger) | Může vyprázdnit analytické údaje |
| [Uživatel DevTest Labs](#devtest-labs-user) | Umožňuje připojit, spustit, restartovat a vypnout virtuální počítače ve vašem Azure DevTest Labs. |
| [Přispěvatel zóny DNS](#dns-zone-contributor) | Umožňuje spravovat zóny a sady záznamů DNS v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup. |
| [Přispěvatel účtu DocumentDB](#documentdb-account-contributor) | Může spravovat účty Azure Cosmos DB. Azure Cosmos DB se dřív jmenovala jako DocumentDB. |
| [EventGrid EventSubscription Přispěvatel](#eventgrid-eventsubscription-contributor) | Umožňuje spravovat operace odběru událostí EventGrid. |
| [Čtecí modul EventSubscription EventGrid](#eventgrid-eventsubscription-reader) | Umožňuje číst odběry událostí EventGrid. |
| [Operátor clusteru HDInsight](#hdinsight-cluster-operator) | Umožňuje číst a upravovat konfigurace clusteru HDInsight. |
| [Přispěvatel doménových služeb HDInsight](#hdinsight-domain-services-contributor) | Může číst, vytvářet, upravovat a odstraňovat služby související s doménami, které jsou potřeba pro HDInsight Balíček zabezpečení podniku |
| [Přispěvatel účtů inteligentních systémů](#intelligent-systems-account-contributor) | Umožňuje správu účtů inteligentních systémů, ale ne přístup k nim. |
| [Přispěvatel Key Vault](#key-vault-contributor) | Umožňuje spravovat trezory klíčů, ale ne přístup k nim. |
| [Autor testovacího prostředí](#lab-creator) | Umožňuje vytvářet, spravovat a odstraňovat spravované laboratoře v rámci účtů Azure Lab. |
| [Přispěvatel Log Analytics](#log-analytics-contributor) | Přispěvatel Log Analytics může číst všechna data monitorování a upravovat nastavení monitorování. Úprava nastavení monitorování zahrnuje přidání rozšíření virtuálního počítače do virtuálních počítačů. čtení klíčů účtu úložiště, aby bylo možné konfigurovat shromažďování protokolů z Azure Storage; vytváření a konfigurace účtů služby Automation; přidávání řešení; a konfigurují se diagnostiky Azure na všech prostředcích Azure. |
| [Čtecí modul Log Analytics](#log-analytics-reader) | Log Analytics čtenář může zobrazit a vyhledat všechna data monitorování a také zobrazit nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. |
| [Přispěvatel aplikace logiky](#logic-app-contributor) | Umožňuje spravovat Logic Apps, ale ne měnit přístup k nim. |
| [Operátor aplikace logiky](#logic-app-operator) | Umožňuje číst, povolit a zakázat Logic Apps, ale nemůže je upravovat ani aktualizovat. |
| [Role operátora spravované aplikace](#managed-application-operator-role) | Umožňuje číst a provádět akce s prostředky spravovaných aplikací. |
| [Čtecí modul spravovaných aplikací](#managed-applications-reader) | Umožňuje číst prostředky ve spravované aplikaci a vyžadovat přístup JIT. |
| [Přispěvatel spravovaných identit](#managed-identity-contributor) | Vytvoření, čtení, aktualizace a odstranění identity přiřazené uživatelem |
| [Spravovaný operátor identity](#managed-identity-operator) | Čtení a přiřazení identity přiřazené uživateli |
| [Přiřazení registrace spravovaných služeb – odstranit roli](#managed-services-registration-assignment-delete-role) | Přiřazení registrace spravovaných služeb – odstranění role umožňuje správcům klienta odstranit přiřazení registrace přiřazené ke svému tenantovi. |
| [Přispěvatel skupiny pro správu](#management-group-contributor) | Role Přispěvatel skupiny pro správu |
| [Čtenář skupiny pro správu](#management-group-reader) | Role čtenář skupiny pro správu |
| [Přispěvatel monitorování](#monitoring-contributor) | Může číst všechna data monitorování a upravovat nastavení monitorování. Přečtěte si také téma [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Monitorování vydavatele metrik](#monitoring-metrics-publisher) | Povoluje publikování metrik pro prostředky Azure. |
| [Čtečka monitorování](#monitoring-reader) | Může číst všechna data monitorování (metriky, protokoly atd.). Přečtěte si také téma [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Přispěvatel sítě](#network-contributor) | Umožňuje spravovat sítě, ale ne přístup k nim. |
| [Přispěvatel nového účtu Relic APM](#new-relic-apm-account-contributor) | Umožňuje správu účtů a aplikací New Relic Application Performance Management, ale ne přístup k nim. |
| [Čtenář a přístup k datům](#reader-and-data-access) | Umožňuje zobrazit vše, ale neumožní vám odstranit ani vytvořit účet úložiště nebo obsažený prostředek. Umožní taky přístup pro čtení a zápis ke všem datům, která jsou obsažená v účtu úložiště, prostřednictvím přístupu k klíčům účtu úložiště. |
| [Přispěvatel Redis Cache](#redis-cache-contributor) | Umožňuje správu mezipamětí Redis, ale ne přístup k nim. |
| [Přispěvatel zásad prostředků (Preview)](#resource-policy-contributor-preview) | Tisk Uživatelé z EA mají oprávnění k vytváření a úpravám zásad prostředků, vytváření lístků podpory a čtení prostředků a hierarchie. |
| [Přispěvatel kolekcí úloh Scheduleru](#scheduler-job-collections-contributor) | Umožňuje spravovat kolekce úloh Scheduleru, ale ne přístup k nim. |
| [Přispěvatel Search Service](#search-service-contributor) | Umožňuje spravovat služby vyhledávání, ale ne přístup k nim. |
| [Správce zabezpečení](#security-admin) | Pouze v Security Center: lze zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení, zavřít výstrahy a doporučení. |
| [Správce zabezpečení (starší verze)](#security-manager-legacy) | Toto je starší role. Místo toho prosím použijte Správce zabezpečení. |
| [Čtecí modul zabezpečení](#security-reader) | Pouze v Security Center: může zobrazit doporučení a výstrahy, zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, ale nemůže provádět změny. |
| [Přispěvatel Site Recovery](#site-recovery-contributor) | Umožňuje správu Site Recovery služby s výjimkou vytvoření trezoru a přiřazení role. |
| [Operátor Site Recovery](#site-recovery-operator) | Umožňuje převzetí služeb při selhání a navrácení služeb po obnovení, ale jiné operace správy Site Recovery. |
| [Čtecí modul Site Recovery](#site-recovery-reader) | Umožňuje zobrazit Site Recovery stav, ale nemůže provádět jiné operace správy. |
| [Přispěvatel účtu prostorových kotev](#spatial-anchors-account-contributor) | Umožňuje spravovat prostorové kotvy ve vašem účtu, ale neodstraňovat je. |
| [Vlastník účtu prostorových ukotvení](#spatial-anchors-account-owner) | Umožňuje spravovat prostorové kotvy v účtu, včetně jejich odstranění. |
| [Čtečka účtu prostorových kotev](#spatial-anchors-account-reader) | Umožňuje vyhledat a číst vlastnosti prostorových ukotvení ve vašem účtu. |
| [Přispěvatel databáze SQL](#sql-db-contributor) | Umožňuje spravovat databáze SQL, ale ne přístup k nim. Nemůžete také spravovat zásady související se zabezpečením nebo jejich nadřazené servery SQL. |
| [Přispěvatel spravované instance SQL](#sql-managed-instance-contributor) | Umožňuje spravovat spravované instance SQL a požadovanou konfiguraci sítě, ale nemůže udělit přístup jiným uživatelům. |
| [Správce zabezpečení SQL](#sql-security-manager) | Umožňuje spravovat zásady týkající se zabezpečení serverů a databází SQL, ale ne přístup k nim. |
| [Přispěvatel SQL Server](#sql-server-contributor) | Umožňuje spravovat servery a databáze SQL, ale ne přístup k nim, a ne jejich zásady související se zabezpečením. |
| [Přispěvatel účtů úložiště](#storage-account-contributor) | Umožňuje správu účtů úložiště. Poskytuje přístup k klíči účtu, který se dá použít pro přístup k datům přes autorizaci pomocí sdíleného klíče. |
| [Role služby operátora klíče účtu úložiště](#storage-account-key-operator-service-role) | Povoluje výpis a opětovné generování přístupových klíčů účtu úložiště. |
| [Přispěvatel dat objektu BLOB služby Storage](#storage-blob-data-contributor) | Čtení, zápis a odstraňování kontejnerů Azure Storage a objektů BLOB. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Vlastník dat objektu BLOB služby Storage](#storage-blob-data-owner) | Poskytuje úplný přístup k Azure Storage kontejnerů a dat objektů blob, včetně přiřazování řízení přístupu k POSIX. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Čtečka dat objektů BLOB úložiště](#storage-blob-data-reader) | Čtení a výpis Azure Storage kontejnerů a objektů BLOB. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Delegování objektu BLOB úložiště](#storage-blob-delegator) | Získejte klíč pro delegování uživatelů, který se pak dá použít k vytvoření sdíleného přístupového podpisu pro kontejner nebo objekt blob, který je podepsaný pomocí přihlašovacích údajů Azure AD. Další informace najdete v tématu [Vytvoření SAS pro delegování uživatelů](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
| [Přispěvatel sdílené složky SMB pro data souborů úložiště](#storage-file-data-smb-share-contributor) | Umožňuje čtení, zápis a odstraňování přístupu v Azure Storage sdílených složkách přes SMB. |
| [Data souboru úložiště – Přispěvatel sdílené složky SMB se zvýšenými oprávněními](#storage-file-data-smb-share-elevated-contributor) | Umožňuje číst, zapisovat, odstraňovat a upravovat přístup k souborům NTFS v Azure Storage sdílených složkách přes SMB. |
| [Čtečka sdílené složky SMB pro data souborů úložiště](#storage-file-data-smb-share-reader) | Povolí přístup pro čtení ke sdílené složce Azure přes SMB. |
| [Přispěvatel dat fronty úložiště](#storage-queue-data-contributor) | Čtení, zápis a odstraňování front Azure Storage a zpráv fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Procesor zpráv s daty ve frontě úložiště](#storage-queue-data-message-processor) | Prohlížet, načítat a odstraňovat zprávy z Azure Storage fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Odesílatel zprávy s daty ve frontě úložiště](#storage-queue-data-message-sender) | Přidejte zprávy do fronty Azure Storage. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Čtečka dat fronty úložiště](#storage-queue-data-reader) | Čtení a výpis Azure Storage front a zpráv fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Přispěvatel žádostí o podporu](#support-request-contributor) | Umožňuje vytvářet a spravovat žádosti o podporu. |
| [Přispěvatel Traffic Manager](#traffic-manager-contributor) | Umožňuje správu profilů Traffic Manager, ale neumožňuje řídit, kdo k nim má přístup. |
| [Správce uživatelských přístupů](#user-access-administrator) | Umožňuje spravovat přístup uživatelů k prostředkům Azure. |
| [Přihlášení správce virtuálního počítače](#virtual-machine-administrator-login) | Zobrazit Virtual Machines na portálu a přihlásit se jako správce |
| [Přispěvatel virtuálních počítačů](#virtual-machine-contributor) | Umožňuje správu virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým se připojuje. |
| [Přihlášení uživatele virtuálního počítače](#virtual-machine-user-login) | Zobrazte Virtual Machines na portálu a přihlaste se jako běžný uživatel. |
| [Přispěvatel webového plánu](#web-plan-contributor) | Umožňuje spravovat webové plány pro weby, ale ne přístup k nim. |
| [Přispěvatel webu](#website-contributor) | Umožňuje spravovat weby (nikoli webové plány), ale ne přístup k nim. |


## <a name="owner"></a>Vlastník
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu všeho, včetně přístupu k prostředkům. |
> | **ID** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Akce** |  |
> | * | Vytváření a Správa prostředků všech typů |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="contributor"></a>Přispěvatel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat vše kromě udělení přístupu k prostředkům. |
> | **ID** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Akce** |  |
> | * | Vytváření a Správa prostředků všech typů |
> | **NotActions** |  |
> | Microsoft. Authorization/*/DELETE | Odstranění rolí, přiřazení zásad, definic zásad a sad zásad |
> | Microsoft. Authorization/*/Write | Vytváření rolí, přiřazení rolí, přiřazování zásad, definic zásad a definic sad zásad |
> | Microsoft. Authorization/elevateAccess/Action | Udělí volajícímu přístup správce přístupu v oboru tenanta. |
> | Microsoft. detail/blueprintAssignments/Write | Vytvořit nebo aktualizovat jakékoli přiřazení podrobného plánu |
> | Microsoft. detail/blueprintAssignments/DELETE | Odstranit všechna přiřazení podrobného plánu |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="reader"></a>Čtenář
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje zobrazit vše, ale ne provádět žádné změny. |
> | **ID** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Akce** |  |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Odstranit ACR |
> | **ID** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Akce** |  |
> | Microsoft. ContainerRegistry/Registry/artefakty/odstranit | Odstraňte artefakt v registru kontejnerů. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | podpis ACR image |
> | **ID** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Akce** |  |
> | Microsoft. ContainerRegistry/Registry/Sign/Write | Metadata vztahu důvěryhodnosti nabízených nebo vyžádaného obsahu pro registr kontejneru. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | ACR pull |
> | **ID** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Akce** |  |
> | Microsoft. ContainerRegistry/registrys/Pull/Read | Vyžádání nebo získání imagí z registru kontejneru. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | nabízení ACR |
> | **ID** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Akce** |  |
> | Microsoft. ContainerRegistry/registrys/Pull/Read | Vyžádání nebo získání imagí z registru kontejneru. |
> | Microsoft. ContainerRegistry/Registry/push/Write | Vložení nebo zápis imagí do registru kontejneru. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | čtečka dat karantény ACR |
> | **ID** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Akce** |  |
> | Microsoft. ContainerRegistry/registrys/karanténa/čtení | Vyžádání nebo získání imagí v karanténě z registru kontejnerů |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | zapisovač dat karantény ACR |
> | **ID** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Akce** |  |
> | Microsoft. ContainerRegistry/registrys/karanténa/čtení | Vyžádání nebo získání imagí v karanténě z registru kontejnerů |
> | Microsoft. ContainerRegistry/registrys/Quarantine/Write | Zápis/Změna stavu karantény imagí v karanténě |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="api-management-service-contributor"></a>Přispěvatel API Management služby
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat službu a rozhraní API. |
> | **ID** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Akce** |  |
> | Microsoft. ApiManagement/Service/* | Vytvoření a Správa služby API Management |
> | Microsoft. Authorization/*/Read | Oprávnění ke čtení |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="api-management-service-operator-role"></a>Role operátora služby API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat službu, ale ne rozhraní API. |
> | **ID** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Akce** |  |
> | Microsoft. ApiManagement/Service/*/Read | Číst API Management instance služby |
> | Microsoft. ApiManagement/Service/Backup/Action | Služba Backup API Management do zadaného kontejneru v účtu úložiště zadaného uživatelem |
> | Microsoft. ApiManagement/Service/DELETE | Odstranit instanci služby API Management |
> | Microsoft. ApiManagement/Service/managedeployments/Action | Změna SKU/jednotek, přidání nebo odebrání regionálních nasazení API Management služby |
> | Microsoft. ApiManagement/Service/Read | Čtení metadat pro instanci služby API Management Service |
> | Microsoft. ApiManagement/Service/Restore/Action | Obnovení služby API Management ze zadaného kontejneru v účtu úložiště zadaného uživatelem |
> | Microsoft. ApiManagement/Service/updatecertificate/Action | Odeslat certifikát SSL pro službu API Management |
> | Microsoft. ApiManagement/Service/updatehostname/Action | Instalace, aktualizace nebo odebrání názvů vlastních domén pro službu API Management |
> | Microsoft. ApiManagement/Service/Write | Vytvoří novou instanci služby API Management. |
> | Microsoft. Authorization/*/Read | Oprávnění ke čtení |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | Microsoft. ApiManagement/Service/Users/Keys/číst | Získání klíčů přidružených k uživateli |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="api-management-service-reader-role"></a>Role čtecího modulu služby API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Přístup ke službě a rozhraním API jen pro čtení |
> | **ID** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Akce** |  |
> | Microsoft. ApiManagement/Service/*/Read | Číst API Management instance služby |
> | Microsoft. ApiManagement/Service/Read | Čtení metadat pro instanci služby API Management Service |
> | Microsoft. Authorization/*/Read | Oprávnění ke čtení |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | Microsoft. ApiManagement/Service/Users/Keys/číst | Získání klíčů přidružených k uživateli |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="application-insights-component-contributor"></a>Přispěvatel Application Insights komponent
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat součásti Application Insights. |
> | **ID** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. Insights/Components/* | Vytváření a Správa komponent Insights |
> | Microsoft. Insights/webtests/* | Vytváření a správa webových testů |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Poskytuje uživateli oprávnění k zobrazení a stažení snímků ladění shromážděných pomocí Snapshot Debugger Application Insights. Všimněte si, že tato oprávnění nejsou zahrnutá v rolích [vlastník](#owner) nebo [Přispěvatel](#contributor) . |
> | **ID** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Insights/Components/*/Read |  |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="automation-job-operator"></a>Operátor úlohy služby Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Vytvářejte a spravujte úlohy pomocí runbooků Automation. |
> | **ID** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read | Přečte Hybrid Runbook Worker prostředky. |
> | Microsoft. Automation/automationAccounts/Jobs/Read | Načte Azure Automation úlohu. |
> | Microsoft. Automation/automationAccounts/Jobs/Resume/Action | Obnoví Azure Automation úlohu. |
> | Microsoft. Automation/automationAccounts/Jobs/stop/Action | Zastaví úlohu Azure Automation. |
> | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Získá datový proud úlohy Azure Automation. |
> | Microsoft. Automation/automationAccounts/Jobs/Suspend/Action | Pozastaví úlohu Azure Automation. |
> | Microsoft. Automation/automationAccounts/Jobs/Write | Vytvoří úlohu Azure Automation. |
> | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Načte výstup úlohy. |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="automation-operator"></a>Operátor služby Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Operátory automatizace můžou spouštět, zastavovat, pozastavovat a obnovovat úlohy. |
> | **ID** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read | Přečte Hybrid Runbook Worker prostředky. |
> | Microsoft. Automation/automationAccounts/Jobs/Read | Načte Azure Automation úlohu. |
> | Microsoft. Automation/automationAccounts/Jobs/Resume/Action | Obnoví Azure Automation úlohu. |
> | Microsoft. Automation/automationAccounts/Jobs/stop/Action | Zastaví úlohu Azure Automation. |
> | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Získá datový proud úlohy Azure Automation. |
> | Microsoft. Automation/automationAccounts/Jobs/Suspend/Action | Pozastaví úlohu Azure Automation. |
> | Microsoft. Automation/automationAccounts/Jobs/Write | Vytvoří úlohu Azure Automation. |
> | Microsoft. Automation/automationAccounts/jobSchedules/Read | Získá Azure Automation plán úlohy. |
> | Microsoft. Automation/automationAccounts/jobSchedules/Write | Vytvoří plán úlohy Azure Automation. |
> | Microsoft. Automation/automationAccounts/linkedWorkspace/Read | Získá pracovní prostor propojený s účtem Automation. |
> | Microsoft. Automation/automationAccounts/Read | Získá účet Azure Automation. |
> | Microsoft. Automation/automationAccounts/Runbooky/číst | Získá Azure Automation Runbook. |
> | Microsoft. Automation/automationAccounts/Schedules/Read | Získá prostředek plánování Azure Automation. |
> | Microsoft. Automation/automationAccounts/Schedules/Write | Vytvoří nebo aktualizuje prostředek plánování Azure Automation. |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Načte výstup úlohy. |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="automation-runbook-operator"></a>Operátor Runbooku služby Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Číst vlastnosti Runbooku – abyste mohli vytvářet úlohy Runbooku. |
> | **ID** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Automation/automationAccounts/Runbooky/číst | Získá Azure Automation Runbook. |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="avere-contributor"></a>Přispěvatel avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může vytvořit a spravovat cluster avere vFXT. |
> | **ID** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. COMPUTE/*/Read |  |
> | Microsoft. COMPUTE/availabilitySets/* |  |
> | Microsoft. COMPUTE/virtualMachines/* |  |
> | Microsoft. COMPUTE/disky/* |  |
> | Microsoft. Network/*/Read |  |
> | Microsoft. Network/networkInterfaces/* |  |
> | Microsoft. Network/virtualNetworks/Read | Získat definici virtuální sítě |
> | Microsoft. Network/virtualNetworks/podsítí/čtení | Získá definici podsítě virtuální sítě. |
> | Microsoft. Network/virtualNetworks/subnets/JOIN/Action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | Microsoft. Network/virtualNetworks/podsítí/joinViaServiceEndpoint/Action | Propojuje prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozorňovat. |
> | Microsoft. Network/networkSecurityGroups/JOIN/Action | Připojí se ke skupině zabezpečení sítě. Nelze upozorňovat. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Storage/*/Read |  |
> | Microsoft. Storage/storageAccounts/* |  |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Resources/Subscriptions/resourceGroups/Resources/Read | Získá prostředky pro skupinu prostředků. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/DELETE | Vrátí výsledek odstranění objektu BLOB. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Read | Vrátí objekt BLOB nebo seznam objektů BLOB. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Write | Vrátí výsledek zápisu objektu BLOB. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="avere-operator"></a>Avere – operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Používá cluster avere vFXT ke správě clusteru. |
> | **ID** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Akce** |  |
> | Microsoft. COMPUTE/virtualMachines/Read | Získá vlastnosti virtuálního počítače. |
> | Microsoft. Network/networkInterfaces/Read | Načte definici síťového rozhraní.  |
> | Microsoft. Network/networkInterfaces/Write | Vytvoří síťové rozhraní nebo aktualizuje stávající síťové rozhraní.  |
> | Microsoft. Network/virtualNetworks/Read | Získat definici virtuální sítě |
> | Microsoft. Network/virtualNetworks/podsítí/čtení | Získá definici podsítě virtuální sítě. |
> | Microsoft. Network/virtualNetworks/subnets/JOIN/Action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | Microsoft. Network/networkSecurityGroups/JOIN/Action | Připojí se ke skupině zabezpečení sítě. Nelze upozorňovat. |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/DELETE | Vrátí výsledek odstranění kontejneru. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/Read | Vrátí seznam kontejnerů. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/Write | Vrátí výsledek Put kontejneru objektů BLOB. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/DELETE | Vrátí výsledek odstranění objektu BLOB. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Read | Vrátí objekt BLOB nebo seznam objektů BLOB. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Write | Vrátí výsledek zápisu objektu BLOB. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-event-hubs-data-owner"></a>Vlastník dat Event Hubs Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje úplný přístup k prostředkům Azure Event Hubs. |
> | **ID** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Akce** |  |
> | Microsoft. EventHub/* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. EventHub/* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-event-hubs-data-receiver"></a>Přijímač dat Event Hubs Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje získat přístup k prostředkům Azure Event Hubs. |
> | **ID** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Akce** |  |
> | Microsoft. EventHub/*/eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. EventHub/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-event-hubs-data-sender"></a>Odesilatel dat Event Hubs Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Povoluje odesílání přístup k prostředkům Azure Event Hubs. |
> | **ID** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Akce** |  |
> | Microsoft. EventHub/*/eventhubs/Read |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. EventHub/*/Send/Action |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Role Správce clusteru služby Azure Kubernetes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Vypíše akci přihlašovacích údajů správce clusteru. |
> | **ID** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Akce** |  |
> | Microsoft. ContainerService/managedClusters/listClusterAdminCredential/Action | Výpis přihlašovacích údajů clusterAdmin spravovaného clusteru |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Role uživatele clusteru služby Azure Kubernetes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Vypíše akci přihlašovacích údajů uživatele clusteru. |
> | **ID** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Akce** |  |
> | Microsoft. ContainerService/managedClusters/listClusterUserCredential/Action | Výpis přihlašovacích údajů clusterUser spravovaného clusteru |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-maps-data-reader-preview"></a>Čtečka dat Azure Maps (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Udělí přístup ke čtení dat souvisejících s mapou z účtu Azure Maps. |
> | **ID** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Akce** |  |
> | *nTato* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Maps/Accounts/data/Read | Udělí datům přístup pro čtení účtu Maps. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-sentinel-contributor"></a>Přispěvatel Sentinel Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Přispěvatel Sentinel Azure |
> | **ID** | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | **Akce** |  |
> | Microsoft. SecurityInsights/* |  |
> | Microsoft. OperationalInsights/pracovní prostory/analýzy/dotaz/akce | Hledání pomocí nového stroje |
> | Microsoft. OperationalInsights/pracovní prostory/číst | Získá existující pracovní prostor. |
> | Microsoft. OperationalInsights/pracovní prostory/savedSearches/* |  |
> | Microsoft. OperationsManagement/Solutions/Read | Získat ukončení řešení OMS |
> | Microsoft. OperationalInsights/pracovní prostory/dotaz/čtení | Spouštění dotazů nad daty v pracovním prostoru |
> | Microsoft. OperationalInsights/pracovní prostory/zdroje dat/číst | Získat zdroje dat v pracovním prostoru. |
> | Microsoft. Insights/sešity/* |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-sentinel-reader"></a>Čtečka Sentinel Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtečka Sentinel Azure |
> | **ID** | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | **Akce** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft. OperationalInsights/pracovní prostory/analýzy/dotaz/akce | Hledání pomocí nového stroje |
> | Microsoft. OperationalInsights/pracovní prostory/číst | Získá existující pracovní prostor. |
> | Microsoft. OperationalInsights/pracovní prostory/savedSearches/číst | Načte uložený vyhledávací dotaz. |
> | Microsoft. OperationsManagement/Solutions/Read | Získat ukončení řešení OMS |
> | Microsoft. OperationalInsights/pracovní prostory/dotaz/čtení | Spouštění dotazů nad daty v pracovním prostoru |
> | Microsoft. OperationalInsights/pracovní prostory/zdroje dat/číst | Získat zdroje dat v pracovním prostoru. |
> | Microsoft. Insights/sešity/číst | Přečíst sešit |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-sentinel-responder"></a>Respondér služby Azure Sentinel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Respondér služby Azure Sentinel |
> | **ID** | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | **Akce** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft. SecurityInsights/případy/* |  |
> | Microsoft. OperationalInsights/pracovní prostory/analýzy/dotaz/akce | Hledání pomocí nového stroje |
> | Microsoft. OperationalInsights/pracovní prostory/číst | Získá existující pracovní prostor. |
> | Microsoft. OperationalInsights/pracovní prostory/zdroje dat/číst | Získat zdroje dat v pracovním prostoru. |
> | Microsoft. OperationalInsights/pracovní prostory/savedSearches/číst | Načte uložený vyhledávací dotaz. |
> | Microsoft. OperationsManagement/Solutions/Read | Získat ukončení řešení OMS |
> | Microsoft. OperationalInsights/pracovní prostory/dotaz/čtení | Spouštění dotazů nad daty v pracovním prostoru |
> | Microsoft. OperationalInsights/pracovní prostory/zdroje dat/číst | Získat zdroje dat v pracovním prostoru. |
> | Microsoft. Insights/sešity/číst | Přečíst sešit |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-service-bus-data-owner"></a>Azure Service Bus vlastník dat
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje úplný přístup k Azure Service Bus prostředkům. |
> | **ID** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Akce** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-service-bus-data-receiver"></a>Azure Service Bus přijímač dat
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje získat přístup k prostředkům Azure Service Bus. |
> | **ID** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Akce** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. ServiceBus/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-service-bus-data-sender"></a>Azure Service Bus odesílatel dat
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje odeslat přístup k prostředkům Azure Service Bus. |
> | **ID** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Akce** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. ServiceBus/*/Send/Action |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="azure-stack-registration-owner"></a>Vlastník registrace Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat Azure Stack registrace. |
> | **ID** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Akce** |  |
> | Microsoft. AzureStack/registrs/Products/* za akci |  |
> | Microsoft. AzureStack/registrs/Products/Read | Získá vlastnosti produktu Azure Stack Marketplace. |
> | Microsoft. AzureStack/registrs/Read | Získá vlastnosti registrace Azure Stack. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="backup-contributor"></a>Přispěvatel zálohování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu služby zálohování, ale nemůže vytvářet trezory a udělovat přístup jiným uživatelům. |
> | **ID** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Network/virtualNetworks/Read | Získat definici virtuální sítě |
> | Microsoft. RecoveryServices/umístění/* |  |
> | Microsoft. RecoveryServices/trezory/backupFabrics/operationResults/* | Správa výsledků operace při správě zálohování |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/* | Vytváření a Správa zálohovacích kontejnerů v rámci záložních prostředků infrastruktury Recovery Services trezoru |
> | Microsoft. RecoveryServices/trezory/backupFabrics/refreshContainers/Action | Aktualizuje seznam kontejnerů. |
> | Microsoft. RecoveryServices/trezory/backupJobs/* | Vytváření a správa úloh zálohování |
> | Microsoft. RecoveryServices/trezory/backupJobsExport/Action | Exportovat úlohy |
> | Microsoft. RecoveryServices/trezory/backupManagementMetaData/* | Vytváření a Správa metadat souvisejících se správou zálohování |
> | Microsoft. RecoveryServices/trezory/backupOperationResults/* | Vytváření a správa výsledků operací správy zálohování |
> | Microsoft. RecoveryServices/trezory/backupPolicies/* | Vytváření a Správa zásad zálohování |
> | Microsoft. RecoveryServices/trezory/backupProtectableItems/* | Vytváření a Správa položek, které se dají zálohovat |
> | Microsoft. RecoveryServices/trezory/backupProtectedItems/* | Vytváření a Správa zálohovaných položek |
> | Microsoft. RecoveryServices/trezory/backupProtectionContainers/* | Vytváření a Správa kontejnerů držících zálohované položky |
> | Microsoft. RecoveryServices/trezory/backupSecurityPIN/* |  |
> | Microsoft. RecoveryServices/trezory/backupUsageSummaries/Read | Vrátí souhrny pro chráněné položky a chráněné servery pro Recovery Services. |
> | Microsoft. RecoveryServices/trezory/certifikáty/* | Vytváření a Správa certifikátů souvisejících se zálohováním v Recovery Services trezoru |
> | Microsoft. RecoveryServices/trezory/extendedInformation/* | Vytváření a Správa rozšířených informací souvisejících s trezorem |
> | Microsoft. RecoveryServices/trezory/monitoringAlerts/Read | Získá výstrahy pro trezor služby Recovery Services. |
> | Microsoft. RecoveryServices/trezory/monitoringConfigurations/* |  |
> | Microsoft. RecoveryServices/trezory/číst | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | Microsoft. RecoveryServices/trezory/registeredIdentities/* | Vytváření a Správa registrovaných identit |
> | Microsoft. RecoveryServices/trezory/použití/* | Vytvoření a správa využití trezoru Recovery Services |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Storage/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft. RecoveryServices/trezory/backupstorageconfig/* |  |
> | Microsoft. RecoveryServices/trezory/backupconfig/* |  |
> | Microsoft. RecoveryServices/trezory/backupValidateOperation/Action | Ověřit operaci pro chráněnou položku |
> | Microsoft. RecoveryServices/trezory/Write | Operace vytvořit trezor vytvoří prostředek Azure typu trezor. |
> | Microsoft. RecoveryServices/trezory/backupOperations/Read | Vrátí stav operace zálohování pro trezor Recovery Services. |
> | Microsoft. RecoveryServices/trezory/backupEngines/Read | Vrátí všechny servery pro správu zálohování zaregistrované s trezorem. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectableContainers/Read | Získat všechny chráněné kontejnery |
> | Microsoft. RecoveryServices/Locations/backupStatus/Action | Zkontroluje stav zálohování pro trezory Recovery Services. |
> | Microsoft. RecoveryServices/Locations/backupPreValidateProtection/Action |  |
> | Microsoft. RecoveryServices/Locations/backupValidateFeatures/Action | Ověřit funkce |
> | Microsoft. RecoveryServices/trezory/monitoringAlerts/Write | Vyřeší upozornění. |
> | Microsoft. RecoveryServices/Operations/Read | Operace vrátí seznam operací pro poskytovatele prostředků. |
> | Microsoft. RecoveryServices/Locations/stav operationstatus/Read | Získá stav operace pro danou operaci. |
> | Microsoft. RecoveryServices/trezory/backupProtectionIntents/Read | Vypsat všechny záložní záměry ochrany |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="backup-operator"></a>Operátor zálohování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu zálohovacích služeb s výjimkou odebrání zálohování, vytváření trezoru a poskytování přístupu jiným uživatelům. |
> | **ID** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Network/virtualNetworks/Read | Získat definici virtuální sítě |
> | Microsoft. RecoveryServices/trezory/backupFabrics/operationResults/Read | Vrátí stav operace. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/operationResults/Read | Načte výsledek operace provedené na kontejneru ochrany. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/Backup/Action | Provede zálohování chráněné položky. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/operationResults/číst | Načte výsledek operace provedené na chráněných položkách. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/operationsStatus/číst | Vrátí stav operace provedené na chráněných položkách. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/Read | Vrátí podrobnosti objektu chráněné položky. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Zřídit rychlé obnovení položky pro chráněnou položku |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/recoveryPoints/číst | Získat body obnovení pro chráněné položky. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | Obnoví body obnovení pro chráněné položky. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Odvolání okamžitého obnovení položky pro chráněnou položku |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/Write | Vytvořit položku chráněnou zálohováním |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/Read | Vrátí všechny registrované kontejnery. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/refreshContainers/Action | Aktualizuje seznam kontejnerů. |
> | Microsoft. RecoveryServices/trezory/backupJobs/* | Vytváření a správa úloh zálohování |
> | Microsoft. RecoveryServices/trezory/backupJobsExport/Action | Exportovat úlohy |
> | Microsoft. RecoveryServices/trezory/backupManagementMetaData/Read |  |
> | Microsoft. RecoveryServices/trezory/backupOperationResults/* | Vytváření a správa výsledků operací správy zálohování |
> | Microsoft. RecoveryServices/trezory/backupPolicies/operationResults/Read | Načte výsledky operace zásad. |
> | Microsoft. RecoveryServices/trezory/backupPolicies/Read | Vrátí všechny zásady ochrany. |
> | Microsoft. RecoveryServices/trezory/backupProtectableItems/* | Vytváření a Správa položek, které se dají zálohovat |
> | Microsoft. RecoveryServices/trezory/backupProtectedItems/Read | Vrátí seznam všech chráněných položek. |
> | Microsoft. RecoveryServices/trezory/backupProtectionContainers/Read | Vrátí všechny kontejnery patřící k předplatnému. |
> | Microsoft. RecoveryServices/trezory/backupUsageSummaries/Read | Vrátí souhrny pro chráněné položky a chráněné servery pro Recovery Services. |
> | Microsoft. RecoveryServices/trezory/certifikáty/zapisovat | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů k prostředku nebo úložišti. |
> | Microsoft. RecoveryServices/trezory/extendedInformation/Read | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | Microsoft. RecoveryServices/trezory/extendedInformation/Write | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | Microsoft. RecoveryServices/trezory/monitoringAlerts/Read | Získá výstrahy pro trezor služby Recovery Services. |
> | Microsoft. RecoveryServices/trezory/monitoringConfigurations/* |  |
> | Microsoft. RecoveryServices/trezory/číst | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | Microsoft. RecoveryServices/trezory/registeredIdentities/operationResults/Read | Operaci získat výsledky operace Get lze použít k získání stavu operace a výsledku asynchronně odeslané operace. |
> | Microsoft. RecoveryServices/trezory/registeredIdentities/Read | Operaci získat kontejnery lze použít k získání kontejnerů zaregistrovaných pro určitý prostředek. |
> | Microsoft. RecoveryServices/trezory/registeredIdentities/Write | Operaci kontejneru registrovat službu lze použít k registraci kontejneru pomocí služby Recovery Services. |
> | Microsoft. RecoveryServices/trezory/používání/čtení | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Storage/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft. RecoveryServices/trezory/backupstorageconfig/* |  |
> | Microsoft. RecoveryServices/trezory/backupValidateOperation/Action | Ověřit operaci pro chráněnou položku |
> | Microsoft. RecoveryServices/trezory/backupOperations/Read | Vrátí stav operace zálohování pro trezor Recovery Services. |
> | Microsoft. RecoveryServices/trezory/backupPolicies/operace/číst | Získá stav operace zásad. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/Write | Vytvoří registrovaný kontejner. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/Dotázat se/Action | Dotazování na úlohy v rámci kontejneru |
> | Microsoft. RecoveryServices/trezory/backupEngines/Read | Vrátí všechny servery pro správu zálohování zaregistrované s trezorem. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/backupProtectionIntent/Write | Vytvořit záložní záměr ochrany |
> | Microsoft. RecoveryServices/trezory/backupFabrics/backupProtectionIntent/Read | Získat záložní záměr ochrany |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectableContainers/Read | Získat všechny chráněné kontejnery |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/Items/Read | Získat všechny položky v kontejneru |
> | Microsoft. RecoveryServices/Locations/backupStatus/Action | Zkontroluje stav zálohování pro trezory Recovery Services. |
> | Microsoft. RecoveryServices/Locations/backupPreValidateProtection/Action |  |
> | Microsoft. RecoveryServices/Locations/backupValidateFeatures/Action | Ověřit funkce |
> | Microsoft. RecoveryServices/trezory/monitoringAlerts/Write | Vyřeší upozornění. |
> | Microsoft. RecoveryServices/Operations/Read | Operace vrátí seznam operací pro poskytovatele prostředků. |
> | Microsoft. RecoveryServices/Locations/stav operationstatus/Read | Získá stav operace pro danou operaci. |
> | Microsoft. RecoveryServices/trezory/backupProtectionIntents/Read | Vypsat všechny záložní záměry ochrany |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="backup-reader"></a>Čtečka zálohování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazovat služby zálohování, ale nemůže provádět změny. |
> | **ID** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | GetAllocatedStamp je interní operace, kterou používá služba. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/operationResults/Read | Vrátí stav operace. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/operationResults/Read | Načte výsledek operace provedené na kontejneru ochrany. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/operationResults/číst | Načte výsledek operace provedené na chráněných položkách. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/operationsStatus/číst | Vrátí stav operace provedené na chráněných položkách. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/Read | Vrátí podrobnosti objektu chráněné položky. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/recoveryPoints/číst | Získat body obnovení pro chráněné položky. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/Read | Vrátí všechny registrované kontejnery. |
> | Microsoft. RecoveryServices/trezory/backupJobs/operationResults/Read | Vrátí výsledek operace úlohy. |
> | Microsoft. RecoveryServices/trezory/backupJobs/Read | Vrátí všechny objekty úlohy. |
> | Microsoft. RecoveryServices/trezory/backupJobsExport/Action | Exportovat úlohy |
> | Microsoft. RecoveryServices/trezory/backupManagementMetaData/Read |  |
> | Microsoft. RecoveryServices/trezory/backupOperationResults/Read | Vrátí výsledek operace zálohování pro Recovery Services trezor. |
> | Microsoft. RecoveryServices/trezory/backupPolicies/operationResults/Read | Načte výsledky operace zásad. |
> | Microsoft. RecoveryServices/trezory/backupPolicies/Read | Vrátí všechny zásady ochrany. |
> | Microsoft. RecoveryServices/trezory/backupProtectedItems/Read | Vrátí seznam všech chráněných položek. |
> | Microsoft. RecoveryServices/trezory/backupProtectionContainers/Read | Vrátí všechny kontejnery patřící k předplatnému. |
> | Microsoft. RecoveryServices/trezory/backupUsageSummaries/Read | Vrátí souhrny pro chráněné položky a chráněné servery pro Recovery Services. |
> | Microsoft. RecoveryServices/trezory/extendedInformation/Read | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | Microsoft. RecoveryServices/trezory/monitoringAlerts/Read | Získá výstrahy pro trezor služby Recovery Services. |
> | Microsoft. RecoveryServices/trezory/číst | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | Microsoft. RecoveryServices/trezory/registeredIdentities/operationResults/Read | Operaci získat výsledky operace Get lze použít k získání stavu operace a výsledku asynchronně odeslané operace. |
> | Microsoft. RecoveryServices/trezory/registeredIdentities/Read | Operaci získat kontejnery lze použít k získání kontejnerů zaregistrovaných pro určitý prostředek. |
> | Microsoft. RecoveryServices/trezory/backupstorageconfig/Read | Vrátí konfiguraci úložiště pro Recovery Services trezor. |
> | Microsoft. RecoveryServices/trezory/backupconfig/Read | Vrátí konfiguraci pro trezor Recovery Services. |
> | Microsoft. RecoveryServices/trezory/backupOperations/Read | Vrátí stav operace zálohování pro trezor Recovery Services. |
> | Microsoft. RecoveryServices/trezory/backupPolicies/operace/číst | Získá stav operace zásad. |
> | Microsoft. RecoveryServices/trezory/backupEngines/Read | Vrátí všechny servery pro správu zálohování zaregistrované s trezorem. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/backupProtectionIntent/Read | Získat záložní záměr ochrany |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/Items/Read | Získat všechny položky v kontejneru |
> | Microsoft. RecoveryServices/Locations/backupStatus/Action | Zkontroluje stav zálohování pro trezory Recovery Services. |
> | Microsoft. RecoveryServices/trezory/monitoringConfigurations/* |  |
> | Microsoft. RecoveryServices/trezory/monitoringAlerts/Write | Vyřeší upozornění. |
> | Microsoft. RecoveryServices/Operations/Read | Operace vrátí seznam operací pro poskytovatele prostředků. |
> | Microsoft. RecoveryServices/Locations/stav operationstatus/Read | Získá stav operace pro danou operaci. |
> | Microsoft. RecoveryServices/trezory/backupProtectionIntents/Read | Vypsat všechny záložní záměry ochrany |
> | Microsoft. RecoveryServices/trezory/používání/čtení | Vrátí podrobnosti využití trezoru Recovery Services. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="billing-reader"></a>Čtenář fakturace
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Povolí přístup pro čtení k fakturačním údajům. |
> | **ID** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. fakturace/*/Read | Přečíst fakturační informace |
> | Microsoft. Commerce/*/Read |  |
> | Microsoft. spotřeb/*/Read |  |
> | Microsoft. Management/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | Microsoft. CostManagement/*/Read |  |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="biztalk-contributor"></a>Přispěvatel BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat BizTalk Services, ale ne přístup k nim. |
> | **ID** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. BizTalkServices/BizTalk/* | Vytváření a Správa BizTalk Services |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="blockchain-member-node-access-preview"></a>Přístup ke členskému uzlu blockchain (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje přístup k blockchain členským uzlům. |
> | **ID** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Akce** |  |
> | Microsoft. blockchain/blockchainMembers/transactionNodes/Read | Získá nebo zobrazí seznam existujících uzlů transakcí členů blockchain. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. blockchain/blockchainMembers/transactionNodes/Connect/Action | Připojí se k uzlu transakce člena blockchain. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="blueprint-contributor"></a>Přispěvatel podrobného plánu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat definice podrobného plánu, ale nepřiřazovat je. |
> | **ID** | 41077137-e803-4205-871c-5a86e6a753b4 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Details/modrotisky/* | Vytvářejte a spravujte definice podrobného plánu nebo artefakty podrobného plánu. |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="blueprint-operator"></a>Operátor podrobného plánu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může přiřadit existující publikované modrotisky, ale nemůže vytvářet nové plány. Poznámka: Tato akce funguje pouze v případě, že je přiřazení provedeno pomocí uživatelem přiřazené spravované identity. |
> | **ID** | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. detail/blueprintAssignments/* | Vytvořte a spravujte přiřazení podrobného plánu. |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cdn-endpoint-contributor"></a>Přispěvatel koncového bodu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat koncové body CDN, ale nemůže udělit přístup jiným uživatelům. |
> | **ID** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. CDN/edgenodes/Read |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/Profiles/Endpoints/* |  |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cdn-endpoint-reader"></a>Čtečka koncového bodu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazovat koncové body CDN, ale nemůže provádět změny. |
> | **ID** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. CDN/edgenodes/Read |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/Profiles/Endpoints/*/Read |  |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cdn-profile-contributor"></a>Přispěvatel profilu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat profily CDN a jejich koncové body, ale nemůže udělit přístup jiným uživatelům. |
> | **ID** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. CDN/edgenodes/Read |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/Profiles/* |  |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cdn-profile-reader"></a>Čtečka profilů CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazit profily CDN a jejich koncové body, ale nemůže provádět změny. |
> | **ID** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. CDN/edgenodes/Read |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/Profiles/*/Read |  |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="classic-network-contributor"></a>Přispěvatel klasických sítí
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu klasických sítí, ale ne přístup k nim. |
> | **ID** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Oprávnění ke čtení |
> | Microsoft. ClassicNetwork/* | Vytváření a Správa klasických sítí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="classic-storage-account-contributor"></a>Přispěvatel klasických účtů úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu klasických účtů úložiště, ale ne přístup k nim. |
> | **ID** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Oprávnění ke čtení |
> | Microsoft. ClassicStorage/storageAccounts/* | Vytvoření a správa účtů úložiště |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Role služby operátora klíče klasického účtu úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Operátoři klíčů pro klasický účet úložiště můžou vypisovat a znovu generovat klíče na klasických účtech úložiště. |
> | **ID** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Akce** |  |
> | Microsoft. ClassicStorage/storageAccounts/klíče listkey/Action | Vypíše přístupové klíče pro účty úložiště. |
> | Microsoft. ClassicStorage/storageAccounts/RegenerateKey/Action | Obnoví existující přístupové klíče pro účet úložiště. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="classic-virtual-machine-contributor"></a>Přispěvatel klasických virtuálních počítačů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu klasických virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým se připojuje. |
> | **ID** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Oprávnění ke čtení |
> | Microsoft. ClassicCompute/domainNames/* | Vytváření a správa názvů klasických výpočetních domén |
> | Microsoft. ClassicCompute/virtualMachines/* | Vytváření a správa virtuálních počítačů |
> | Microsoft. ClassicNetwork/networkSecurityGroups/JOIN/Action |  |
> | Microsoft. ClassicNetwork/reservedIps/Link/Action | Propojení rezervované IP adresy |
> | Microsoft. ClassicNetwork/reservedIps/Read | Načte rezervované IP adresy. |
> | Microsoft. ClassicNetwork/virtualNetworks/JOIN/Action | Připojí se k virtuální síti. |
> | Microsoft. ClassicNetwork/virtualNetworks/Read | Získat virtuální síť. |
> | Microsoft. ClassicStorage/storageAccounts/disks/Read | Vrátí disk účtu úložiště. |
> | Microsoft. ClassicStorage/storageAccounts/images/Read | Vrátí image účtu úložiště. Zastaralé. Použijte Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | Microsoft. ClassicStorage/storageAccounts/klíče listkey/Action | Vypíše přístupové klíče pro účty úložiště. |
> | Microsoft. ClassicStorage/storageAccounts/Read | Vrátí účet úložiště s daným účtem. |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cognitive-services-contributor"></a>Přispěvatel Cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vytvářet, číst, aktualizovat, odstraňovat a spravovat klíče Cognitive Services. |
> | **ID** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Cognitiveservices Account/* |  |
> | Microsoft. Features/Features/Read | Získá funkce předplatného. |
> | Microsoft. Features/Providers/Features/Read | Získá funkci předplatného v daném poskytovateli prostředků. |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo přečte nastavení diagnostiky pro Analysis Server |
> | Microsoft. Insights/logDefinitions/Read | Čtení definic protokolů |
> | Microsoft. Insights/metricdefinitions/Read | Čtení definic metriky |
> | Microsoft. Insights/metriky/číst | Čtení metrik |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/nasazení/operace/čtení | Načte nebo vypíše operace nasazení. |
> | Microsoft. Resources/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | Microsoft. Resources/Subscriptions/Read | Získá seznam předplatných. |
> | Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/* |  |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cognitive-services-data-reader-preview"></a>Čtečka dat Cognitive Services (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst Cognitive Services data. |
> | **ID** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Akce** |  |
> | *nTato* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Cognitiveservices Account/*/Read |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cognitive-services-user"></a>Cognitive Services uživatel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst a zobrazit seznam klíčů Cognitive Services. |
> | **ID** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Akce** |  |
> | Microsoft. Cognitiveservices Account/*/Read |  |
> | Microsoft. Cognitiveservices Account/Accounts/klíče listkey/Action | Zobrazit seznam klíčů |
> | Microsoft. Insights/alertRules/Read | Čtení klasické výstrahy metriky |
> | Microsoft. Insights/diagnosticSettings/Read | Přečíst nastavení diagnostiky prostředků |
> | Microsoft. Insights/logDefinitions/Read | Čtení definic protokolů |
> | Microsoft. Insights/metricdefinitions/Read | Čtení definic metriky |
> | Microsoft. Insights/metriky/číst | Čtení metrik |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/operace/čtení | Načte nebo vypíše operace nasazení. |
> | Microsoft. Resources/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | Microsoft. Resources/Subscriptions/Read | Získá seznam předplatných. |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Cognitiveservices Account/* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cosmos-db-account-reader-role"></a>Role čtečky účtu Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může číst data Azure Cosmos DB účtu. Správa účtů Azure Cosmos DB najdete v tématu [Přispěvatel účtu DocumentDB](#documentdb-account-contributor) . |
> | **ID** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí může mít oprávnění ke čtení pro jednotlivé uživatele. |
> | Microsoft. DocumentDB/*/Read | Čtení libovolné kolekce |
> | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Action | Přečte klíče databázového účtu jen pro čtení. |
> | Microsoft. Insights/MetricDefinitions/Read | Čtení definic metriky |
> | Microsoft. Insights/metriky/číst | Čtení metrik |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cosmos-db-operator"></a>Operátor Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu účtů Azure Cosmos DB, ale ne přístup k datům v nich. Zabraňuje přístup k klíčům účtu a připojovacím řetězcům. |
> | **ID** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Akce** |  |
> | Microsoft. DocumentDb/databaseAccounts/* |  |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | Microsoft. DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft. DocumentDB/databaseAccounts/klíče listkey/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může odeslat žádost o obnovení pro Cosmos DB databázi nebo kontejner pro účet. |
> | **ID** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Akce** |  |
> | Microsoft. DocumentDB/databaseAccounts/Backup/Action | Odeslat žádost o konfiguraci zálohování |
> | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Odeslat žádost o obnovení |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cost-management-contributor"></a>Přispěvatel Cost Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje zobrazit náklady a spravovat konfiguraci nákladů (např. rozpočty, exporty). |
> | **ID** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Akce** |  |
> | Microsoft. spotřeb/* |  |
> | Microsoft. CostManagement/* |  |
> | Microsoft. fakturace/billingPeriods/Read |  |
> | Microsoft. Resources/Subscriptions/Read | Získá seznam předplatných. |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Advisor/Configurations/Ready | Získat konfigurace |
> | Microsoft. Advisor/doporučení/číst | Čte doporučení |
> | Microsoft. Management/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="cost-management-reader"></a>Čtecí modul Cost Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazit data a konfiguraci nákladů (např. rozpočty, exporty). |
> | **ID** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Akce** |  |
> | Microsoft. spotřeb/*/Read |  |
> | Microsoft. CostManagement/*/Read |  |
> | Microsoft. fakturace/billingPeriods/Read |  |
> | Microsoft. Resources/Subscriptions/Read | Získá seznam předplatných. |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Advisor/Configurations/Ready | Získat konfigurace |
> | Microsoft. Advisor/doporučení/číst | Čte doporučení |
> | Microsoft. Management/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="data-box-contributor"></a>Přispěvatel Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat všechno, co služba Data Box, s výjimkou poskytnutí přístupu jiným uživatelům. |
> | **ID** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Databox/* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="data-box-reader"></a>Čtecí modul Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu Data Box služby s výjimkou vytváření pořadí nebo úprav podrobností objednávky a udělení přístupu jiným uživatelům. |
> | **ID** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Databox/*/Read |  |
> | Microsoft. Databox/Jobs/listsecrets/Action |  |
> | Microsoft. Databox/Jobs/listcredentials/Action | Zobrazí nešifrované přihlašovací údaje související s objednávkou. |
> | Microsoft. Databox/Locations/availableSkus/Action | Tato metoda vrátí seznam dostupných skladových položek. |
> | Microsoft. Databox/Locations/validateAddress/Action | Ověří dodací adresu a v případě potřeby poskytne alternativní adresy. |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="data-factory-contributor"></a>Přispěvatel Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Vytváření a Správa datových továren a také podřízených prostředků v nich. |
> | **ID** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. DataFactory/DataFactory/* | Vytváření a Správa datových továrn a podřízených prostředků v nich. |
> | Microsoft. DataFactory/továrny/* | Vytváření a Správa datových továrn a podřízených prostředků v nich. |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="data-lake-analytics-developer"></a>Vývojář Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje odesílat, monitorovat a spravovat vlastní úlohy, ale neumožňuje vytvářet ani odstraňovat Data Lake Analytics účty. |
> | **ID** | 47b7735b-770e-4598-A7DA-8b91488b4c88 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. BigAnalytics/účty/* |  |
> | Microsoft. DataLakeAnalytics/účty/* |  |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | Microsoft. BigAnalytics/Accounts/DELETE |  |
> | Microsoft. BigAnalytics/Accounts/TakeOwnership/Action |  |
> | Microsoft. BigAnalytics/Accounts/Write |  |
> | Microsoft. DataLakeAnalytics/Accounts/DELETE | Odstraňte účet DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/Accounts/TakeOwnership/Action | Udělte oprávnění ke zrušení úloh odeslaných jinými uživateli. |
> | Microsoft. DataLakeAnalytics/Accounts/Write | Vytvořte nebo aktualizujte účet DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/Accounts/dataLakeStoreAccounts/Write | Vytvoří nebo aktualizuje propojený účet DataLakeStore účtu DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/Accounts/dataLakeStoreAccounts/DELETE | Odpojte účet DataLakeStore z účtu DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/Accounts/storageAccounts/Write | Vytvoří nebo aktualizuje propojený účet úložiště účtu DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/Accounts/storageAccounts/DELETE | Odpojte účet úložiště od účtu DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/Accounts/firewallRules/Write | Vytvořte nebo aktualizujte pravidlo brány firewall. |
> | Microsoft. DataLakeAnalytics/Accounts/firewallRules/DELETE | Odstraní pravidlo brány firewall. |
> | Microsoft. DataLakeAnalytics/Accounts/computePolicies/Write | Vytvořte nebo aktualizujte výpočetní zásadu. |
> | Microsoft. DataLakeAnalytics/Accounts/computePolicies/DELETE | Odstraňte výpočetní zásadu. |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="data-purger"></a>Nástroj pro vyprázdnění dat
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může vyprázdnit analytické údaje |
> | **ID** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Akce** |  |
> | Microsoft. Insights/Components/*/Read |  |
> | Microsoft. Insights/komponenty/vyprázdnění/akce | Vymazání dat z Application Insights |
> | Microsoft. OperationalInsights/pracovní prostory/*/Read |  |
> | Microsoft. OperationalInsights/pracovní prostory/vyprázdnit/akce | Odstranit zadaná data z pracovního prostoru |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="devtest-labs-user"></a>Uživatel DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje připojit, spustit, restartovat a vypnout virtuální počítače ve vašem Azure DevTest Labs. |
> | **ID** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. COMPUTE/availabilitySets/Read | Získá vlastnosti skupiny dostupnosti. |
> | Microsoft. COMPUTE/virtualMachines/*/Read | Přečtěte si vlastnosti virtuálního počítače (velikosti virtuálních počítačů, běhový stav, rozšíření virtuálních počítačů atd.). |
> | Microsoft. COMPUTE/virtualMachines/unallocate/Action | Vyžádá virtuální počítač a uvolní výpočetní prostředky. |
> | Microsoft. COMPUTE/virtualMachines/Read | Získá vlastnosti virtuálního počítače. |
> | Microsoft. COMPUTE/virtualMachines/restart/Action | Restartuje virtuální počítač. |
> | Microsoft. COMPUTE/virtualMachines/Start/Action | Spustí virtuální počítač. |
> | Microsoft. DevTestLab/*/Read | Čtení vlastností testovacího prostředí |
> | Microsoft. DevTestLab/Labs/claimAnyVm/Action | Vydeklarujte náhodný virtuální počítač v testovacím prostředí. |
> | Microsoft. DevTestLab/Labs/createEnvironment/Action | Vytvářejte virtuální počítače v testovacím prostředí. |
> | Microsoft. DevTestLab/Labs/ensureCurrentUserProfile/Action | Ujistěte se, že aktuální uživatel má v testovacím prostředí platný profil. |
> | Microsoft. DevTestLab/Labs/vzorce/odstranit | Odstraňte vzorce. |
> | Microsoft. DevTestLab/Labs/vzorců/čtení | Čtení vzorců. |
> | Microsoft. DevTestLab/Labs/vzorce/zápis | Přidat nebo upravit vzorce. |
> | Microsoft. DevTestLab/Labs/policySets/evaluatePolicies/Action | Vyhodnotí zásady testovacího prostředí. |
> | Microsoft. DevTestLab/Labs/virtualMachines/nárok/Action | Převezme vlastnictví existujícího virtuálního počítače. |
> | Microsoft. DevTestLab/Labs/VirtualMachines/listApplicableSchedules/Action | Zobrazí seznam použitelných plánů spuštění/zastavení, pokud existují. |
> | Microsoft. DevTestLab/Labs/virtualMachines/getRdpFileContents/Action | Načte řetězec, který představuje obsah souboru RDP pro virtuální počítač. |
> | Microsoft. Network/loadBalancers/backendAddressPools/JOIN/Action | Připojí fond adres back-endu nástroje pro vyrovnávání zatížení. Nelze upozorňovat. |
> | Microsoft. Network/loadBalancers/inboundNatRules/JOIN/Action | Připojí příchozí pravidlo NAT pro vyrovnávání zatížení. Nelze upozorňovat. |
> | Microsoft. Network/networkInterfaces/*/Read | Čtení vlastností síťového rozhraní (například všech nástrojů pro vyrovnávání zatížení, které jsou součástí síťového rozhraní) |
> | Microsoft. Network/networkInterfaces/JOIN/Action | Připojí virtuální počítač k síťovému rozhraní. Nelze upozorňovat. |
> | Microsoft. Network/networkInterfaces/Read | Načte definici síťového rozhraní.  |
> | Microsoft. Network/networkInterfaces/Write | Vytvoří síťové rozhraní nebo aktualizuje stávající síťové rozhraní.  |
> | Microsoft. Network/publicIPAddresses/*/Read | Čtení vlastností veřejné IP adresy |
> | Microsoft. Network/publicIPAddresses/JOIN/Action | Připojí se k veřejné IP adrese. Nelze upozorňovat. |
> | Microsoft. Network/publicIPAddresses/Read | Získá definici veřejné IP adresy. |
> | Microsoft. Network/virtualNetworks/subnets/JOIN/Action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | Microsoft. Resources/nasazení/operace/čtení | Načte nebo vypíše operace nasazení. |
> | Microsoft. Resources/nasazení/čtení | Načte nebo vypíše nasazení. |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Storage/storageAccounts/klíče listkey/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | **NotActions** |  |
> | Microsoft. COMPUTE/virtualMachines/povolených velikostí/Read | Zobrazí seznam dostupných velikostí, na které se dá virtuální počítač aktualizovat. |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="dns-zone-contributor"></a>Přispěvatel zóny DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat zóny a sady záznamů DNS v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup. |
> | **ID** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. Network/dnsZones/* | Vytváření a Správa záznamů a zón DNS |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="documentdb-account-contributor"></a>Přispěvatel účtu DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat účty Azure Cosmos DB. Azure Cosmos DB se dřív jmenovala jako DocumentDB. |
> | **ID** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. DocumentDb/databaseAccounts/* | Vytváření a Správa účtů Azure Cosmos DB |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription Přispěvatel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat operace odběru událostí EventGrid. |
> | **ID** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. EventGrid/eventSubscriptions/* |  |
> | Microsoft. EventGrid/topicTypes/eventSubscriptions/Read | Vypsat globální odběry událostí podle typu tématu |
> | Microsoft. EventGrid/Locations/eventSubscriptions/Read | Výpis odběrů místních událostí |
> | Microsoft. EventGrid/Locations/topicTypes/eventSubscriptions/Read | Výpis odběrů místních událostí podle TopicType |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="eventgrid-eventsubscription-reader"></a>Čtecí modul EventSubscription EventGrid
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst odběry událostí EventGrid. |
> | **ID** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. EventGrid/eventSubscriptions/Read | Přečíst eventSubscription |
> | Microsoft. EventGrid/topicTypes/eventSubscriptions/Read | Vypsat globální odběry událostí podle typu tématu |
> | Microsoft. EventGrid/Locations/eventSubscriptions/Read | Výpis odběrů místních událostí |
> | Microsoft. EventGrid/Locations/topicTypes/eventSubscriptions/Read | Výpis odběrů místních událostí podle TopicType |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="hdinsight-cluster-operator"></a>Operátor clusteru HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst a upravovat konfigurace clusteru HDInsight. |
> | **ID** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Akce** |  |
> | Microsoft. HDInsight/*/Read |  |
> | Microsoft. HDInsight/Clusters/getGatewaySettings/Action | Získat nastavení brány pro cluster HDInsight |
> | Microsoft. HDInsight/Clusters/updateGatewaySettings/Action | Aktualizace nastavení brány pro cluster HDInsight |
> | Microsoft. HDInsight/clustery/konfigurace/* |  |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Resources/nasazení/operace/čtení | Načte nebo vypíše operace nasazení. |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="hdinsight-domain-services-contributor"></a>Přispěvatel doménových služeb HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může číst, vytvářet, upravovat a odstraňovat služby související s doménami, které jsou potřeba pro HDInsight Balíček zabezpečení podniku |
> | **ID** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Akce** |  |
> | Microsoft. AAD/*/Read |  |
> | Microsoft. AAD/domainServices/*/Read |  |
> | Microsoft. AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="intelligent-systems-account-contributor"></a>Přispěvatel účtů inteligentních systémů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu účtů inteligentních systémů, ale ne přístup k nim. |
> | **ID** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. IntelligentSystems/účty/* | Vytváření a Správa účtů inteligentních systémů |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="key-vault-contributor"></a>Přispěvatel Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat trezory klíčů, ale ne přístup k nim. |
> | **ID** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Trezor Microsoft. |  |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | Microsoft. webdeletedVaults trezor/Locations//vyprázdnění/Action | Vymazání tichého odstraněného trezoru klíčů |
> | Microsoft. hsmPools – trezor//* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="lab-creator"></a>Autor testovacího prostředí
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vytvářet, spravovat a odstraňovat spravované laboratoře v rámci účtů Azure Lab. |
> | **ID** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. LabServices/labAccounts/*/Read |  |
> | Microsoft. LabServices/labAccounts/createLab/Action | Vytvořte testovací prostředí v účtu testovacího prostředí. |
> | Microsoft. LabServices/labAccounts/sizes/getRegionalAvailability/Action |  |
> | Microsoft. LabServices/labAccounts/getRegionalAvailability/Action | Získat informace o regionální dostupnosti pro každou kategorii velikostí nakonfigurovanou v rámci účtu testovacího prostředí |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="log-analytics-contributor"></a>Přispěvatel Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Přispěvatel Log Analytics může číst všechna data monitorování a upravovat nastavení monitorování. Úprava nastavení monitorování zahrnuje přidání rozšíření virtuálního počítače do virtuálních počítačů. čtení klíčů účtu úložiště, aby bylo možné konfigurovat shromažďování protokolů z Azure Storage; vytváření a konfigurace účtů služby Automation; přidávání řešení; a konfigurují se diagnostiky Azure na všech prostředcích Azure. |
> | **ID** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Akce** |  |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | Microsoft. Automation/automationAccounts/* |  |
> | Microsoft. ClassicCompute/virtualMachines/Extensions/* |  |
> | Microsoft. ClassicStorage/storageAccounts/klíče listkey/Action | Vypíše přístupové klíče pro účty úložiště. |
> | Microsoft. COMPUTE/virtualMachines/Extensions/* |  |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo přečte nastavení diagnostiky pro Analysis Server |
> | Microsoft. OperationalInsights/* |  |
> | Microsoft. OperationsManagement/* |  |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/* |  |
> | Microsoft. Storage/storageAccounts/klíče listkey/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="log-analytics-reader"></a>Čtenář Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Log Analytics čtenář může zobrazit a vyhledat všechna data monitorování a také zobrazit nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. |
> | **ID** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Akce** |  |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | Microsoft. OperationalInsights/pracovní prostory/analýzy/dotaz/akce | Hledání pomocí nového stroje |
> | Microsoft. OperationalInsights/pracovní prostory/hledání/akce | Spustí vyhledávací dotaz. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | Microsoft. OperationalInsights/pracovní prostory/sharedKeys/číst | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="logic-app-contributor"></a>Přispěvatel aplikace logiky
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat Logic Apps, ale ne měnit přístup k nim. |
> | **ID** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. ClassicStorage/storageAccounts/klíče listkey/Action | Vypíše přístupové klíče pro účty úložiště. |
> | Microsoft. ClassicStorage/storageAccounts/Read | Vrátí účet úložiště s daným účtem. |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Insights/metricAlerts/* |  |
> | Microsoft. Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo přečte nastavení diagnostiky pro Analysis Server |
> | Microsoft. Insights/logdefinitions/* | Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. Vypíše kategorie protokolů v protokolu aktivit. |
> | Microsoft. Insights/metricDefinitions/* | Čtení definic metriky (seznam dostupných typů metrik pro prostředek). |
> | Microsoft. Logic/* | Spravuje prostředky Logic Apps. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Storage/storageAccounts/klíče listkey/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft. Storage/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Web/connectionGateways/* | Vytvoření a správa brány připojení. |
> | Microsoft. Web/připojení/* | Vytvoření a Správa připojení. |
> | Microsoft. Web/customApis/* | Vytvoří a spravuje vlastní rozhraní API. |
> | Microsoft. Web/serverových farem/JOIN/Action |  |
> | Microsoft. Web/serverových farem/Read | Získat vlastnosti v plánu App Service |
> | Microsoft. Web/Sites/Functions/listSecrets/Action | Výpis tajných kódů funkcí |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="logic-app-operator"></a>Operátor aplikace logiky
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst, povolit a zakázat Logic Apps, ale nemůže je upravovat ani aktualizovat. |
> | **ID** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/*/Read | Pravidla výstrah pro čtení přehledů |
> | Microsoft. Insights/metricAlerts/*/Read |  |
> | Microsoft. Insights/diagnosticSettings/*/Read | Získá nastavení diagnostiky pro Logic Apps |
> | Microsoft. Insights/metricDefinitions/*/Read | Získá dostupné metriky pro Logic Apps. |
> | Microsoft. Logic/*/Read | Přečte Logic Apps prostředky. |
> | Microsoft. Logic/Workflows/Disable/Action | Zakáže pracovní postup. |
> | Microsoft. Logic/Workflows/Enable/Action | Povolí pracovní postup. |
> | Microsoft. Logic/Workflows/Validate/Action | Ověří pracovní postup. |
> | Microsoft. Resources/nasazení/operace/čtení | Načte nebo vypíše operace nasazení. |
> | Microsoft. Resources/Subscriptions/operationresults/Read | Získejte výsledky operace předplatného. |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Web/connectionGateways/*/Read | Čtení bran připojení. |
> | Microsoft. Web/Connections/*/Read | Čtení připojení. |
> | Microsoft. Web/customApis/*/Read | Číst vlastní rozhraní API. |
> | Microsoft. Web/serverových farem/Read | Získat vlastnosti v plánu App Service |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="managed-application-operator-role"></a>Role operátora spravované aplikace
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst a provádět akce s prostředky spravovaných aplikací. |
> | **ID** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Akce** |  |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | Microsoft. Solutions/aplikace/číst | Načte seznam aplikací. |
> | Microsoft. Solutions/* za akci |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="managed-applications-reader"></a>Čtecí modul spravovaných aplikací
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst prostředky ve spravované aplikaci a vyžadovat přístup JIT. |
> | **ID** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Akce** |  |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="managed-identity-contributor"></a>Přispěvatel spravovaných identit
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Vytvoření, čtení, aktualizace a odstranění identity přiřazené uživatelem |
> | **ID** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Akce** |  |
> | Microsoft. ManagedIdentity/userAssignedIdentities/Read | Získá existující identitu přiřazenou uživatelem. |
> | Microsoft. ManagedIdentity/userAssignedIdentities/Write | Vytvoří novou identitu přiřazenou uživatelem nebo aktualizuje značky přidružené k existující identitě přiřazené uživatelem. |
> | Microsoft. ManagedIdentity/userAssignedIdentities/DELETE | Odstraní existující identitu přiřazenou uživatelem. |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="managed-identity-operator"></a>Spravovaný operátor identity
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení a přiřazení identity přiřazené uživateli |
> | **ID** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Akce** |  |
> | Microsoft. ManagedIdentity/userAssignedIdentities/*/Read |  |
> | Microsoft. ManagedIdentity/userAssignedIdentities/*/Assign/Action |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="managed-services-registration-assignment-delete-role"></a>Přiřazení registrace spravovaných služeb – odstranit roli
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Přiřazení registrace spravovaných služeb – odstranění role umožňuje správcům klienta odstranit přiřazení registrace přiřazené ke svému tenantovi. |
> | **ID** | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | **Akce** |  |
> | Microsoft. ManagedServices/registrationAssignments/Read | Načte seznam přiřazení registrace spravovaných služeb. |
> | Microsoft. ManagedServices/registrationAssignments/DELETE | Odebere přiřazení registrace spravovaných služeb. |
> | Microsoft. ManagedServices/operationStatuses/Read | Přečte stav operace pro daný prostředek. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="management-group-contributor"></a>Přispěvatel skupiny pro správu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Role Přispěvatel skupiny pro správu |
> | **ID** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Akce** |  |
> | Microsoft. Management/managementGroups/DELETE | Odstraní skupinu pro správu. |
> | Microsoft. Management/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | Microsoft. Management/managementGroups/Subscriptions/DELETE | Odpojí předplatné ze skupiny pro správu. |
> | Microsoft. Management/managementGroups/Subscriptions/Write | Přidruží stávající předplatné ke skupině pro správu. |
> | Microsoft. Management/managementGroups/Write | Vytvoří nebo aktualizuje skupinu pro správu. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="management-group-reader"></a>Čtenář skupiny pro správu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Role čtenář skupiny pro správu |
> | **ID** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Akce** |  |
> | Microsoft. Management/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="monitoring-contributor"></a>Přispěvatel monitorování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může číst všechna data monitorování a upravovat nastavení monitorování. Přečtěte si také téma [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **ID** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Akce** |  |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | Microsoft. AlertsManagement/výstrahy/* |  |
> | Microsoft. AlertsManagement/alertsSummary/* |  |
> | Microsoft. Insights/actiongroups/* |  |
> | Microsoft. Insights/Upozorněníprotokoluaktivit/* |  |
> | Microsoft. Insights/AlertRules/* | Pravidla upozornění pro čtení, zápis a odstranění. |
> | Microsoft. Insights/Components/* | Čtení, zápis a odstraňování komponent Application Insights. |
> | Microsoft. Insights/DiagnosticSettings/* | Nastavení diagnostiky pro čtení, zápis a odstranění |
> | Microsoft. Insights/EventTypes/* | Vypíše události protokolu aktivit (události správy) v předplatném. Toto oprávnění platí pro programový i portálový přístup k protokolu aktivit. |
> | Microsoft. Insights/LogDefinitions/* | Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. Vypíše kategorie protokolů v protokolu aktivit. |
> | Microsoft. Insights/metricalerts/* |  |
> | Microsoft. Insights/MetricDefinitions/* | Čtení definic metriky (seznam dostupných typů metrik pro prostředek). |
> | Microsoft. Insights/metriky/* | Načte metriky pro prostředek. |
> | Microsoft. Insights/registr/Action | Registrace poskytovatele Microsoft Insights |
> | Microsoft. Insights/scheduledqueryrules/* |  |
> | Microsoft. Insights/webtests/* | Čtení, zápis a odstranění Application Insights webových testů. |
> | Microsoft. Insights/sešity/* |  |
> | Microsoft. OperationalInsights/pracovní prostory/intelligencepacks/* | Balíčky řešení Log Analytics pro čtení, zápis a odstranění. |
> | Microsoft. OperationalInsights/pracovní prostory/savedSearches/* | Čtení, zápis a odstraňování uložených hledání Log Analytics. |
> | Microsoft. OperationalInsights/pracovní prostory/hledání/akce | Spustí vyhledávací dotaz. |
> | Microsoft. OperationalInsights/pracovní prostory/sharedKeys/Action | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Microsoft. OperationalInsights/pracovní prostory/storageinsightconfigs/* | Čtení, zápis a odstraňování konfigurací Log Analytics úložiště |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. monitor zátěže byl/monitores/* |  |
> | Microsoft. monitor zátěže byl/notificationSettings/* |  |
> | Microsoft. AlertsManagement/smartDetectorAlertRules/* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="monitoring-metrics-publisher"></a>Monitorování vydavatele metrik
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Povoluje publikování metrik pro prostředky Azure. |
> | **ID** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Akce** |  |
> | Microsoft. Insights/registr/Action | Registrace poskytovatele Microsoft Insights |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Insights/metriky/Write | Zapsat metriky |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="monitoring-reader"></a>Čtečka monitorování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může číst všechna data monitorování (metriky, protokoly atd.). Přečtěte si také téma [Začínáme s rolemi, oprávněními a zabezpečením pomocí Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **ID** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Akce** |  |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | Microsoft. OperationalInsights/pracovní prostory/hledání/akce | Spustí vyhledávací dotaz. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="network-contributor"></a>Přispěvatel sítě
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat sítě, ale ne přístup k nim. |
> | **ID** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. Network/* | Vytváření a správa sítí |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="new-relic-apm-account-contributor"></a>Přispěvatel nového účtu Relic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu účtů a aplikací New Relic Application Performance Management, ale ne přístup k nim. |
> | **ID** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | NewRelic. APM/účty/* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="reader-and-data-access"></a>Čtenář a přístup k datům
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje zobrazit vše, ale neumožní vám odstranit ani vytvořit účet úložiště nebo obsažený prostředek. Umožní taky přístup pro čtení a zápis ke všem datům, která jsou obsažená v účtu úložiště, prostřednictvím přístupu k klíčům účtu úložiště. |
> | **ID** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Akce** |  |
> | Microsoft. Storage/storageAccounts/klíče listkey/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft. Storage/storageAccounts/ListAccountSas/Action | Vrátí token SAS účtu pro zadaný účet úložiště. |
> | Microsoft. Storage/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="redis-cache-contributor"></a>Přispěvatel Redis Cache
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu mezipamětí Redis, ale ne přístup k nim. |
> | **ID** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. cache/Redis/* | Vytváření a Správa mezipamětí Redis |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="resource-policy-contributor-preview"></a>Přispěvatel zásad prostředků (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Tisk Uživatelé z EA mají oprávnění k vytváření a úpravám zásad prostředků, vytváření lístků podpory a čtení prostředků a hierarchie. |
> | **ID** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Akce** |  |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | Microsoft. Authorization/policyassignments/* | Vytvoření a správa přiřazení zásad |
> | Microsoft. Authorization/PolicyDefinitions/* | Vytváření a Správa definic zásad |
> | Microsoft. Authorization/policysetdefinitions/* | Vytváření a Správa sad zásad |
> | Microsoft. PolicyInsights/* |  |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="scheduler-job-collections-contributor"></a>Přispěvatel kolekcí úloh Scheduleru
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat kolekce úloh Scheduleru, ale ne přístup k nim. |
> | **ID** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Scheduler/jobcollections/* | Vytváření a Správa kolekcí úloh |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="search-service-contributor"></a>Přispěvatel Search Service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat služby vyhledávání, ale ne přístup k nim. |
> | **ID** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Search/searchServices/* | Vytváření a správa vyhledávacích služeb |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="security-admin"></a>Správce zabezpečení
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Pouze v Security Center: lze zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení, zavřít výstrahy a doporučení. |
> | **ID** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Authorization/policyAssignments/* | Vytvoření a správa přiřazení zásad |
> | Microsoft. Authorization/policyDefinitions/* | Vytváření a Správa definic zásad |
> | Microsoft. Authorization/policySetDefinitions/* | Vytváření a Správa sad zásad |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. Management/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | Microsoft. operationalInsights/pracovní prostory/*/Read | Zobrazení dat Log Analytics |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Security/* |  |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="security-manager-legacy"></a>Správce zabezpečení (starší verze)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Toto je starší role. Místo toho prosím použijte Správce zabezpečení. |
> | **ID** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. ClassicCompute/*/Read | Čtení informací o konfiguraci klasických virtuálních počítačů |
> | Microsoft. ClassicCompute/virtualMachines/*/Write | Zápis konfigurace pro klasické virtuální počítače |
> | Microsoft. ClassicNetwork/*/Read | Přečíst informace o konfiguraci klasické sítě |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Security/* | Vytváření a Správa bezpečnostních komponent a zásad |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="security-reader"></a>Čtecí modul zabezpečení
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Pouze v Security Center: může zobrazit doporučení a výstrahy, zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, ale nemůže provádět změny. |
> | **ID** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. operationalInsights/pracovní prostory/*/Read | Zobrazení dat Log Analytics |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Security/*/Read | Číst součásti a zásady zabezpečení |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Management/managementGroups/Read | Vypíše skupiny pro správu ověřeného uživatele. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="site-recovery-contributor"></a>Přispěvatel Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu Site Recovery služby s výjimkou vytvoření trezoru a přiřazení role. |
> | **ID** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. Network/virtualNetworks/Read | Získat definici virtuální sítě |
> | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | GetAllocatedStamp je interní operace, kterou používá služba. |
> | Microsoft. RecoveryServices/Locations/allocateStamp/Action | AllocateStamp je interní operace, kterou používá služba. |
> | Microsoft. RecoveryServices/trezory/certifikáty/zapisovat | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů k prostředku nebo úložišti. |
> | Microsoft. RecoveryServices/trezory/extendedInformation/* | Vytváření a Správa rozšířených informací souvisejících s trezorem |
> | Microsoft. RecoveryServices/trezory/číst | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | Microsoft. RecoveryServices/trezory/refreshContainers/Read |  |
> | Microsoft. RecoveryServices/trezory/registeredIdentities/* | Vytváření a Správa registrovaných identit |
> | Microsoft. RecoveryServices/trezory/replicationAlertSettings/* | Vytvořit nebo aktualizovat nastavení výstrah replikace |
> | Microsoft. RecoveryServices/trezory/replicationEvents/Read | Čtení jakýchkoli událostí |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/* | Vytváření a Správa prostředků infrastruktury replikace |
> | Microsoft. RecoveryServices/trezory/replicationJobs/* | Vytváření a správa úloh replikace |
> | Microsoft. RecoveryServices/trezory/replicationPolicies/* | Vytváření a Správa zásad replikace |
> | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/* | Vytváření a Správa plánů obnovení |
> | Microsoft. RecoveryServices/trezory/storageConfig/* | Vytvoření a Správa konfigurace úložiště Recovery Servicesho trezoru |
> | Microsoft. RecoveryServices/trezory/tokenInfo/Read |  |
> | Microsoft. RecoveryServices/trezory/používání/čtení | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft. RecoveryServices/trezory/vaultTokens/Read | Operaci tokenu trezoru jde použít k získání tokenu trezoru pro operace back-endu na úrovni trezoru. |
> | Microsoft. RecoveryServices/trezory/monitoringAlerts/* | Čtení výstrah pro trezor služby Recovery Services |
> | Microsoft. RecoveryServices/trezory/monitoringConfigurations/notificationConfiguration/Read |  |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Storage/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="site-recovery-operator"></a>Operátor Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje převzetí služeb při selhání a navrácení služeb po obnovení, ale jiné operace správy Site Recovery. |
> | **ID** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. Network/virtualNetworks/Read | Získat definici virtuální sítě |
> | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | GetAllocatedStamp je interní operace, kterou používá služba. |
> | Microsoft. RecoveryServices/Locations/allocateStamp/Action | AllocateStamp je interní operace, kterou používá služba. |
> | Microsoft. RecoveryServices/trezory/extendedInformation/Read | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | Microsoft. RecoveryServices/trezory/číst | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | Microsoft. RecoveryServices/trezory/refreshContainers/Read |  |
> | Microsoft. RecoveryServices/trezory/registeredIdentities/operationResults/Read | Operaci získat výsledky operace Get lze použít k získání stavu operace a výsledku asynchronně odeslané operace. |
> | Microsoft. RecoveryServices/trezory/registeredIdentities/Read | Operaci získat kontejnery lze použít k získání kontejnerů zaregistrovaných pro určitý prostředek. |
> | Microsoft. RecoveryServices/trezory/replicationAlertSettings/Read | Přečtěte si všechna nastavení výstrah. |
> | Microsoft. RecoveryServices/trezory/replicationEvents/Read | Čtení jakýchkoli událostí |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/checkConsistency/Action | Kontroluje konzistenci prostředků infrastruktury. |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/Read | Čtení jakýchkoli prostředků infrastruktury |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/reassociateGateway/Action | Znovu přidružit bránu |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/renewcertificate/Action | Prodloužit platnost certifikátu pro Fabric |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationNetworks/Read | Čtení všech sítí |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Číst všechna mapování sítě |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/Read | Čtení všech kontejnerů ochrany |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Čtení všech chráněných položek |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/Action | Použít bod obnovení |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/Action | Potvrzení převzetí služeb při selhání |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/Action | Plánované převzetí služeb při selhání |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Čtení jakýchkoli chráněných položek |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/číst | Čtení všech bodů obnovení replikace |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/Action | Opravit replikaci |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reprotect/Action | Znovu chránit chráněnou položku |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Přepnout kontejner ochrany |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/Action | Testovací převzetí služeb při selhání |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/Action | Vyčištění testovacího převzetí služeb při selhání |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Převzetí služeb při selhání |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/Action | Aktualizovat službu mobility |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Přečíst všechna mapování kontejnerů ochrany |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationRecoveryServicesProviders/Read | Číst všechny poskytovatele Recovery Services |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Aktualizovat zprostředkovatele |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/Read | Čtení všech klasifikací úložiště |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Čtení jakýchkoli mapování klasifikací úložiště |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationvCenters/Read | Číst libovolný servery vCenter |
> | Microsoft. RecoveryServices/trezory/replicationJobs/* | Vytváření a správa úloh replikace |
> | Microsoft. RecoveryServices/trezory/replicationPolicies/Read | Čtení všech zásad |
> | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/failoverCommit/Action | Plán obnovení potvrzení převzetí služeb při selhání |
> | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/plannedFailover/Action | Plán obnovení plánovaného převzetí služeb při selhání |
> | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/Read | Čtení všech plánů obnovení |
> | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/reprotect/Action | Znovu zapnout ochranu plánu obnovení |
> | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/testFailover/Action | Plán obnovení testovacího převzetí služeb při selhání |
> | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/testFailoverCleanup/Action | Plán obnovení pro vyčištění testovacího převzetí služeb při selhání |
> | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/unplannedFailover/Action | Plán obnovení převzetí služeb při selhání |
> | Microsoft. RecoveryServices/trezory/monitoringAlerts/* | Čtení výstrah pro trezor služby Recovery Services |
> | Microsoft. RecoveryServices/trezory/monitoringConfigurations/notificationConfiguration/Read |  |
> | Microsoft. RecoveryServices/trezory/storageConfig/Read |  |
> | Microsoft. RecoveryServices/trezory/tokenInfo/Read |  |
> | Microsoft. RecoveryServices/trezory/používání/čtení | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft. RecoveryServices/trezory/vaultTokens/Read | Operaci tokenu trezoru jde použít k získání tokenu trezoru pro operace back-endu na úrovni trezoru. |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Storage/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="site-recovery-reader"></a>Čtecí modul Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje zobrazit Site Recovery stav, ale nemůže provádět jiné operace správy. |
> | **ID** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | GetAllocatedStamp je interní operace, kterou používá služba. |
> | Microsoft. RecoveryServices/trezory/extendedInformation/Read | Operace získat rozšířené informace získá rozšířené informace o objektu, které představují prostředek Azure typu? trezor? |
> | Microsoft. RecoveryServices/trezory/monitoringAlerts/Read | Získá výstrahy pro trezor služby Recovery Services. |
> | Microsoft. RecoveryServices/trezory/monitoringConfigurations/notificationConfiguration/Read |  |
> | Microsoft. RecoveryServices/trezory/číst | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | Microsoft. RecoveryServices/trezory/refreshContainers/Read |  |
> | Microsoft. RecoveryServices/trezory/registeredIdentities/operationResults/Read | Operaci získat výsledky operace Get lze použít k získání stavu operace a výsledku asynchronně odeslané operace. |
> | Microsoft. RecoveryServices/trezory/registeredIdentities/Read | Operaci získat kontejnery lze použít k získání kontejnerů zaregistrovaných pro určitý prostředek. |
> | Microsoft. RecoveryServices/trezory/replicationAlertSettings/Read | Přečtěte si všechna nastavení výstrah. |
> | Microsoft. RecoveryServices/trezory/replicationEvents/Read | Čtení jakýchkoli událostí |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/Read | Čtení jakýchkoli prostředků infrastruktury |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationNetworks/Read | Čtení všech sítí |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Číst všechna mapování sítě |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/Read | Čtení všech kontejnerů ochrany |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Čtení všech chráněných položek |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Čtení jakýchkoli chráněných položek |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/číst | Čtení všech bodů obnovení replikace |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Přečíst všechna mapování kontejnerů ochrany |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationRecoveryServicesProviders/Read | Číst všechny poskytovatele Recovery Services |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/Read | Čtení všech klasifikací úložiště |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Čtení jakýchkoli mapování klasifikací úložiště |
> | Microsoft. RecoveryServices/trezory/replicationFabrics/replicationvCenters/Read | Číst libovolný servery vCenter |
> | Microsoft. RecoveryServices/trezory/replicationJobs/Read | Čtení všech úloh |
> | Microsoft. RecoveryServices/trezory/replicationPolicies/Read | Čtení všech zásad |
> | Microsoft. RecoveryServices/trezory/replicationRecoveryPlans/Read | Čtení všech plánů obnovení |
> | Microsoft. RecoveryServices/trezory/storageConfig/Read |  |
> | Microsoft. RecoveryServices/trezory/tokenInfo/Read |  |
> | Microsoft. RecoveryServices/trezory/používání/čtení | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft. RecoveryServices/trezory/vaultTokens/Read | Operaci tokenu trezoru jde použít k získání tokenu trezoru pro operace back-endu na úrovni trezoru. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="spatial-anchors-account-contributor"></a>Přispěvatel účtu prostorových kotev
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat prostorové kotvy ve vašem účtu, ale neodstraňovat je. |
> | **ID** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Akce** |  |
> | *nTato* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Vytvořit prostorové kotvy |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Vyhledat nejbližší prostorové kotvy |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/– vlastnosti/číst | Získat vlastnosti prostorových ukotvení |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/dotaz/číst | Vyhledat prostorové kotvy |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Odeslat diagnostická data, která vám pomůžou zlepšit kvalitu služby prostorových kotev Azure |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Aktualizovat vlastnosti prostorových ukotvení |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="spatial-anchors-account-owner"></a>Vlastník účtu prostorových ukotvení
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat prostorové kotvy v účtu, včetně jejich odstranění. |
> | **ID** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Akce** |  |
> | *nTato* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Vytvořit prostorové kotvy |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/DELETE | Odstranit prostorové kotvy |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Vyhledat nejbližší prostorové kotvy |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/– vlastnosti/číst | Získat vlastnosti prostorových ukotvení |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/dotaz/číst | Vyhledat prostorové kotvy |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Odeslat diagnostická data, která vám pomůžou zlepšit kvalitu služby prostorových kotev Azure |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Aktualizovat vlastnosti prostorových ukotvení |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="spatial-anchors-account-reader"></a>Čtečka účtu prostorových kotev
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vyhledat a číst vlastnosti prostorových ukotvení ve vašem účtu. |
> | **ID** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Akce** |  |
> | *nTato* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Vyhledat nejbližší prostorové kotvy |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/– vlastnosti/číst | Získat vlastnosti prostorových ukotvení |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/dotaz/číst | Vyhledat prostorové kotvy |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Odeslat diagnostická data, která vám pomůžou zlepšit kvalitu služby prostorových kotev Azure |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="sql-db-contributor"></a>Přispěvatel databáze SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat databáze SQL, ale ne přístup k nim. Nemůžete také spravovat zásady související se zabezpečením nebo jejich nadřazené servery SQL. |
> | **ID** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. SQL/Locations/*/Read |  |
> | Microsoft. SQL/servery/databáze/* | Vytváření a Správa databází SQL |
> | Microsoft. SQL/servery/číst | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Insights/metriky/číst | Čtení metrik |
> | Microsoft. Insights/metricDefinitions/Read | Čtení definic metriky |
> | **NotActions** |  |
> | Microsoft. SQL/managedInstances/databáze/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/schémata/tabulky/sloupce/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/databáze/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/servery/databáze/auditingPolicies/* | Upravit zásady auditu |
> | Microsoft. SQL/servery/databáze/auditingSettings/* | Upravit nastavení auditu |
> | Microsoft. SQL/servery/databáze/auditRecords/Read | Načtení záznamů auditu objektu BLOB databáze |
> | Microsoft. SQL/servery/databáze/connectionPolicies/* | Upravit zásady připojení |
> | Microsoft. SQL/servery/databáze/currentSensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/dataMaskingPolicies/* | Upravit zásady maskování dat |
> | Microsoft. SQL/servery/databáze/extendedAuditingSettings/* |  |
> | Microsoft. SQL/servery/databáze/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/schémata/tabulky/sloupce/sensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/securityAlertPolicies/* | Upravit zásady výstrah zabezpečení |
> | Microsoft. SQL/servery/databáze/securityMetrics/* | Upravit metriky zabezpečení |
> | Microsoft. SQL/servery/databáze/sensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/servery/databáze/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/servery/databáze/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/servery/vulnerabilityAssessments/* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="sql-managed-instance-contributor"></a>Přispěvatel spravované instance SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat spravované instance SQL a požadovanou konfiguraci sítě, ale nemůže udělit přístup jiným uživatelům. |
> | **ID** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Akce** |  |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Network/networkSecurityGroups/* |  |
> | Microsoft. Network/routeTables/* |  |
> | Microsoft. SQL/Locations/*/Read |  |
> | Microsoft. SQL/managedInstances/* |  |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Network/virtualNetworks/podsítí/* |  |
> | Microsoft. Network/virtualNetworks/* |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Insights/metriky/číst | Čtení metrik |
> | Microsoft. Insights/metricDefinitions/Read | Čtení definic metriky |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="sql-security-manager"></a>Správce zabezpečení SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat zásady týkající se zabezpečení serverů a databází SQL, ale ne přístup k nim. |
> | **ID** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Přečíst autorizaci Microsoftu |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Network/virtualNetworks/podsítí/joinViaServiceEndpoint/Action | Propojuje prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozorňovat. |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. SQL/managedInstances/databáze/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/schémata/tabulky/sloupce/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/databáze/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/databáze/transparentDataEncryption/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/servery/auditingPolicies/* | Vytváření a Správa zásad auditování SQL serveru |
> | Microsoft. SQL/servery/auditingSettings/* | Vytvoření a Správa nastavení auditování SQL serveru |
> | Microsoft. SQL/servery/extendedAuditingSettings/Read | Načíst podrobnosti o zásadách auditování rozšířených objektů BLOB serveru nakonfigurovaných na daném serveru |
> | Microsoft. SQL/servery/databáze/auditingPolicies/* | Vytvoření a Správa zásad auditování databáze systému SQL Server |
> | Microsoft. SQL/servery/databáze/auditingSettings/* | Vytvoření a Správa nastavení auditování databáze systému SQL Server |
> | Microsoft. SQL/servery/databáze/auditRecords/Read | Čtení záznamů auditu |
> | Microsoft. SQL/servery/databáze/connectionPolicies/* | Vytváření a Správa zásad připojení k databázi SQL serveru |
> | Microsoft. SQL/servery/databáze/currentSensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/dataMaskingPolicies/* | Vytváření a Správa zásad pro maskování dat databáze SQL serveru |
> | Microsoft. SQL/servery/databáze/extendedAuditingSettings/Read | Načte podrobnosti o rozšířených zásadách auditování objektů BLOB konfigurovaných pro danou databázi. |
> | Microsoft. SQL/servery/databáze/číst | Vrátí seznam databází nebo získá vlastnosti zadané databáze. |
> | Microsoft. SQL/servery/databáze/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/schémata/čtení | Získá schéma databáze. |
> | Microsoft. SQL/servery/databáze/schémata/tabulky/sloupce/čtení | Získá sloupec databáze. |
> | Microsoft. SQL/servery/databáze/schémata/tabulky/sloupce/sensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/schémata/tabulky/čtení | Získat databázovou tabulku. |
> | Microsoft. SQL/servery/databáze/securityAlertPolicies/* | Vytváření a Správa zásad výstrah zabezpečení databáze serveru SQL Server |
> | Microsoft. SQL/servery/databáze/securityMetrics/* | Vytváření a Správa metrik zabezpečení databáze systému SQL Server |
> | Microsoft. SQL/servery/databáze/sensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/transparentDataEncryption/* |  |
> | Microsoft. SQL/servery/databáze/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/servery/databáze/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/servery/databáze/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/servery/firewallRules/* |  |
> | Microsoft. SQL/servery/číst | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Microsoft. SQL/servery/securityAlertPolicies/* | Vytváření a Správa zásad výstrah zabezpečení systému SQL Server |
> | Microsoft. SQL/servery/vulnerabilityAssessments/* |  |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="sql-server-contributor"></a>Přispěvatel SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat servery a databáze SQL, ale ne přístup k nim, a ne jejich zásady související se zabezpečením. |
> | **ID** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. SQL/Locations/*/Read |  |
> | Microsoft. SQL/servery/* | Vytváření a Správa serverů SQL |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Insights/metriky/číst | Čtení metrik |
> | Microsoft. Insights/metricDefinitions/Read | Čtení definic metriky |
> | **NotActions** |  |
> | Microsoft. SQL/managedInstances/databáze/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/schémata/tabulky/sloupce/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/databáze/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databáze/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/servery/auditingPolicies/* | Upravit zásady auditování SQL serveru |
> | Microsoft. SQL/servery/auditingSettings/* | Upravit nastavení auditování SQL serveru |
> | Microsoft. SQL/servery/databáze/auditingPolicies/* | Upravit zásady auditování databáze systému SQL Server |
> | Microsoft. SQL/servery/databáze/auditingSettings/* | Upravit nastavení auditování databáze systému SQL Server |
> | Microsoft. SQL/servery/databáze/auditRecords/Read | Čtení záznamů auditu |
> | Microsoft. SQL/servery/databáze/connectionPolicies/* | Upravit zásady připojení databáze SQL serveru |
> | Microsoft. SQL/servery/databáze/currentSensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/dataMaskingPolicies/* | Upravit zásady maskování dat databáze systému SQL Server |
> | Microsoft. SQL/servery/databáze/extendedAuditingSettings/* |  |
> | Microsoft. SQL/servery/databáze/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/schémata/tabulky/sloupce/sensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/securityAlertPolicies/* | Upravit zásady výstrah zabezpečení databáze systému SQL Server |
> | Microsoft. SQL/servery/databáze/securityMetrics/* | Upravit metriky zabezpečení databáze systému SQL Server |
> | Microsoft. SQL/servery/databáze/sensitivityLabels/* |  |
> | Microsoft. SQL/servery/databáze/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/servery/databáze/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/servery/databáze/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/servery/extendedAuditingSettings/* |  |
> | Microsoft. SQL/servery/securityAlertPolicies/* | Upravit zásady výstrah zabezpečení systému SQL Server |
> | Microsoft. SQL/servery/vulnerabilityAssessments/* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-account-contributor"></a>Přispěvatel účtu úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu účtů úložiště. Poskytuje přístup k klíči účtu, který se dá použít pro přístup k datům přes autorizaci pomocí sdíleného klíče. |
> | **ID** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení všech autorizací |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Insights/diagnosticSettings/* | Spravovat nastavení diagnostiky |
> | Microsoft. Network/virtualNetworks/podsítí/joinViaServiceEndpoint/Action | Propojuje prostředek, jako je například účet úložiště nebo databáze SQL, do podsítě. Nelze upozorňovat. |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. Storage/storageAccounts/* | Vytvoření a správa účtů úložiště |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-account-key-operator-service-role"></a>Role služby operátora klíče účtu úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Povoluje výpis a opětovné generování přístupových klíčů účtu úložiště. |
> | **ID** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Akce** |  |
> | Microsoft. Storage/storageAccounts/klíče listkey/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft. Storage/storageAccounts/RegenerateKey/Action | Znovu vygeneruje přístupové klíče pro zadaný účet úložiště. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-blob-data-contributor"></a>Přispěvatel dat objektu BLOB služby Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení, zápis a odstraňování kontejnerů Azure Storage a objektů BLOB. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **ID** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Akce** |  |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/DELETE | Odstraní kontejner. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/Read | Vrátí kontejner nebo seznam kontejnerů. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/Write | Úprava metadat nebo vlastností kontejneru. |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Vrátí klíč delegování uživatele pro Blob service. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/DELETE | Odstraní objekt BLOB. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Read | Vrátí objekt BLOB nebo seznam objektů BLOB. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Write | Zápis do objektu BLOB. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-blob-data-owner"></a>Vlastník dat objektu BLOB služby Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Poskytuje úplný přístup k Azure Storage kontejnerů a dat objektů blob, včetně přiřazování řízení přístupu k POSIX. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **ID** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Akce** |  |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/* | Úplná oprávnění pro kontejnery |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Vrátí klíč delegování uživatele pro Blob service. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/* | Úplná oprávnění pro objekty blob |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-blob-data-reader"></a>Čtečka dat objektů BLOB úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení a výpis Azure Storage kontejnerů a objektů BLOB. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **ID** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Akce** |  |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/Read | Vrátí kontejner nebo seznam kontejnerů. |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Vrátí klíč delegování uživatele pro Blob service. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/BLOBs/Read | Vrátí objekt BLOB nebo seznam objektů BLOB. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-blob-delegator"></a>Delegování objektu BLOB úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Získejte klíč pro delegování uživatelů, který se pak dá použít k vytvoření sdíleného přístupového podpisu pro kontejner nebo objekt blob, který je podepsaný pomocí přihlašovacích údajů Azure AD. Další informace najdete v tématu [Vytvoření SAS pro delegování uživatelů](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
> | **ID** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Akce** |  |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Vrátí klíč delegování uživatele pro Blob service. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-file-data-smb-share-contributor"></a>Přispěvatel sdílené složky SMB pro data souborů úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje čtení, zápis a odstraňování přístupu v Azure Storage sdílených složkách přes SMB. |
> | **ID** | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | **Akce** |  |
> | *nTato* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/služby souborů/sdílené složky/soubory/čtení | Vrátí soubor nebo složku nebo seznam souborů nebo složek. |
> | Microsoft. Storage/storageAccounts/Souborová služba/sdílené složky/soubory/zápis | Vrátí výsledek zápisu souboru nebo vytvoření složky. |
> | Microsoft. Storage/storageAccounts/služby souborů/sdílené složky/soubory/odstranění | Vrátí výsledek odstranění souboru nebo složky. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-file-data-smb-share-elevated-contributor"></a>Data souboru úložiště – Přispěvatel sdílené složky SMB se zvýšenými oprávněními
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst, zapisovat, odstraňovat a upravovat přístup k souborům NTFS v Azure Storage sdílených složkách přes SMB. |
> | **ID** | a7264617-510b-434b-a828-9731dc254ea7 |
> | **Akce** |  |
> | *nTato* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/služby souborů/sdílené složky/soubory/čtení | Vrátí soubor nebo složku nebo seznam souborů nebo složek. |
> | Microsoft. Storage/storageAccounts/Souborová služba/sdílené složky/soubory/zápis | Vrátí výsledek zápisu souboru nebo vytvoření složky. |
> | Microsoft. Storage/storageAccounts/služby souborů/sdílené složky/soubory/odstranění | Vrátí výsledek odstranění souboru nebo složky. |
> | Microsoft. Storage/storageAccounts/služby souborů/sdílené složky/soubory/modifypermissions/Action | Vrátí výsledek úpravy oprávnění pro soubor nebo složku. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-file-data-smb-share-reader"></a>Čtečka sdílené složky SMB pro data souborů úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Povolí přístup pro čtení ke sdílené složce Azure přes SMB. |
> | **ID** | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | **Akce** |  |
> | *nTato* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/služby souborů/sdílené složky/soubory/čtení | Vrátí soubor nebo složku nebo seznam souborů nebo složek. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-queue-data-contributor"></a>Přispěvatel dat fronty úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení, zápis a odstraňování front Azure Storage a zpráv fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **ID** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Akce** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/DELETE | Odstraní frontu. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Read | Vrátí frontu nebo seznam front. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Write | Úprava metadat nebo vlastností fronty |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/DELETE | Odstraní jednu nebo více zpráv z fronty. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Read | Prohlížet nebo načíst jednu nebo více zpráv z fronty. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Write | Přidá zprávu do fronty. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-queue-data-message-processor"></a>Procesor zpráv s daty ve frontě úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Prohlížet, načítat a odstraňovat zprávy z Azure Storage fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **ID** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Akce** |  |
> | *nTato* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Read | Prohlížet zprávu |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Process/Action | Načte a odstraní zprávu. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-queue-data-message-sender"></a>Odesílatel zprávy s daty ve frontě úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Přidejte zprávy do fronty Azure Storage. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **ID** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Akce** |  |
> | *nTato* |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Add/Action | Přidá zprávu do fronty. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="storage-queue-data-reader"></a>Čtečka dat fronty úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení a výpis Azure Storage front a zpráv fronty. Chcete-li zjistit, které akce jsou pro danou datovou operaci požadovány, přečtěte si téma [oprávnění pro volání operací s daty objektů BLOB a front](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **ID** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Akce** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Read | Vrátí frontu nebo seznam front. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Messages/Read | Prohlížet nebo načíst jednu nebo více zpráv z fronty. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="support-request-contributor"></a>Přispěvatel žádostí o podporu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vytvářet a spravovat žádosti o podporu. |
> | **ID** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Oprávnění ke čtení |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="traffic-manager-contributor"></a>Přispěvatel Traffic Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu profilů Traffic Manager, ale neumožňuje řídit, kdo k nim má přístup. |
> | **ID** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Čtení rolí a přiřazení rolí |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Network/trafficManagerProfiles/* |  |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="user-access-administrator"></a>Správce přístupu uživatelů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat přístup uživatelů k prostředkům Azure. |
> | **ID** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Akce** |  |
> | */read | Přečte prostředky všech typů s výjimkou tajných klíčů. |
> | Microsoft. Authorization/* | Spravovat autorizaci |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="virtual-machine-administrator-login"></a>Přihlášení správce virtuálního počítače
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Zobrazit Virtual Machines na portálu a přihlásit se jako správce |
> | **ID** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Akce** |  |
> | Microsoft. Network/publicIPAddresses/Read | Získá definici veřejné IP adresy. |
> | Microsoft. Network/virtualNetworks/Read | Získat definici virtuální sítě |
> | Microsoft. Network/loadBalancers/Read | Získá definici nástroje pro vyrovnávání zatížení. |
> | Microsoft. Network/networkInterfaces/Read | Načte definici síťového rozhraní.  |
> | Microsoft. COMPUTE/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. COMPUTE/virtualMachines/Login/Action | Přihlaste se k virtuálnímu počítači jako běžný uživatel. |
> | Microsoft. COMPUTE/virtualMachines/loginAsAdmin/Action | Přihlášení k virtuálnímu počítači pomocí oprávnění správce Windows nebo kořenového uživatele platformy Linux |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="virtual-machine-contributor"></a>Přispěvatel virtuálních počítačů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým se připojuje. |
> | **ID** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Oprávnění ke čtení |
> | Microsoft. COMPUTE/availabilitySets/* | Vytváření a Správa skupin dostupnosti výpočtů |
> | Microsoft. COMPUTE/umístění/* | Vytváření a Správa výpočetních míst |
> | Microsoft. COMPUTE/virtualMachines/* | Vytváření a správa virtuálních počítačů |
> | Microsoft. COMPUTE/virtualMachineScaleSets/* | Vytváření a správa virtuálních počítačů Scale Sets |
> | Microsoft. DevTestLab/plánuje/* |  |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Network/applicationGateways/backendAddressPools/JOIN/Action | Připojí se ke fondu back-end adres služby Application Gateway. Nelze upozorňovat. |
> | Microsoft. Network/loadBalancers/backendAddressPools/JOIN/Action | Připojí fond adres back-endu nástroje pro vyrovnávání zatížení. Nelze upozorňovat. |
> | Microsoft. Network/loadBalancers/inboundNatPools/JOIN/Action | Připojí příchozí fond NAT pro vyrovnávání zatížení. Nelze upozorňovat. |
> | Microsoft. Network/loadBalancers/inboundNatRules/JOIN/Action | Připojí příchozí pravidlo NAT pro vyrovnávání zatížení. Nelze upozorňovat. |
> | Microsoft. Network/loadBalancers/probs/JOIN/Action | Umožňuje používat sondy nástroje pro vyrovnávání zatížení. Například s touto vlastností oprávnění healthProbe sady VM scaleing můžete odkazovat na test. Nelze upozorňovat. |
> | Microsoft. Network/loadBalancers/Read | Získá definici nástroje pro vyrovnávání zatížení. |
> | Microsoft. Network/umístění/* | Vytváření a Správa síťových umístění |
> | Microsoft. Network/networkInterfaces/* | Vytváření a Správa síťových rozhraní |
> | Microsoft. Network/networkSecurityGroups/JOIN/Action | Připojí se ke skupině zabezpečení sítě. Nelze upozorňovat. |
> | Microsoft. Network/networkSecurityGroups/Read | Získá definici skupiny zabezpečení sítě. |
> | Microsoft. Network/publicIPAddresses/JOIN/Action | Připojí se k veřejné IP adrese. Nelze upozorňovat. |
> | Microsoft. Network/publicIPAddresses/Read | Získá definici veřejné IP adresy. |
> | Microsoft. Network/virtualNetworks/Read | Získat definici virtuální sítě |
> | Microsoft. Network/virtualNetworks/subnets/JOIN/Action | Připojí se k virtuální síti. Nelze upozorňovat. |
> | Microsoft. RecoveryServices/umístění/* |  |
> | Microsoft. RecoveryServices/trezory/backupFabrics/backupProtectionIntent/Write | Vytvořit záložní záměr ochrany |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/*/Read |  |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/Read | Vrátí podrobnosti objektu chráněné položky. |
> | Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/Write | Vytvořit položku chráněnou zálohováním |
> | Microsoft. RecoveryServices/trezory/backupPolicies/Read | Vrátí všechny zásady ochrany. |
> | Microsoft. RecoveryServices/trezory/backupPolicies/Write | Vytvoří zásady ochrany. |
> | Microsoft. RecoveryServices/trezory/číst | Operace získat úložiště Získá objekt, který představuje prostředek Azure typu trezor. |
> | Microsoft. RecoveryServices/trezory/používání/čtení | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft. RecoveryServices/trezory/Write | Operace vytvořit trezor vytvoří prostředek Azure typu trezor. |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. SqlVirtualMachine/* |  |
> | Microsoft. Storage/storageAccounts/klíče listkey/Action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft. Storage/storageAccounts/Read | Vrátí seznam účtů úložiště nebo získá vlastnosti pro zadaný účet úložiště. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="virtual-machine-user-login"></a>Přihlášení uživatele virtuálního počítače
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Zobrazte Virtual Machines na portálu a přihlaste se jako běžný uživatel. |
> | **ID** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Akce** |  |
> | Microsoft. Network/publicIPAddresses/Read | Získá definici veřejné IP adresy. |
> | Microsoft. Network/virtualNetworks/Read | Získat definici virtuální sítě |
> | Microsoft. Network/loadBalancers/Read | Získá definici nástroje pro vyrovnávání zatížení. |
> | Microsoft. Network/networkInterfaces/Read | Načte definici síťového rozhraní.  |
> | Microsoft. COMPUTE/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | Microsoft. COMPUTE/virtualMachines/Login/Action | Přihlaste se k virtuálnímu počítači jako běžný uživatel. |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="web-plan-contributor"></a>Přispěvatel webového plánu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat webové plány pro weby, ale ne přístup k nim. |
> | **ID** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Oprávnění ke čtení |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Web/serverových farem/* | Vytváření a Správa serverových farem |
> | Microsoft. Web/hostingEnvironments/JOIN/Action | Připojí se k App Service Environment. |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="website-contributor"></a>Přispěvatel webu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat weby (nikoli webové plány), ale ne přístup k nim. |
> | **ID** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Akce** |  |
> | Microsoft. Authorization/*/Read | Oprávnění ke čtení |
> | Microsoft. Insights/alertRules/* | Vytváření a Správa pravidel výstrah pro přehledy |
> | Microsoft. Insights/Components/* | Vytváření a Správa komponent Insights |
> | Microsoft. ResourceHealth/availabilityStatuses/Read | Získá stavy dostupnosti pro všechny prostředky v zadaném oboru. |
> | Microsoft. Resources/nasazení/* | Vytváření a Správa nasazení skupin prostředků |
> | Microsoft. Resources/Subscriptions/resourceGroups/Read | Načte nebo vypíše skupiny prostředků. |
> | Microsoft. support/* | Vytváření a Správa lístků podpory |
> | Microsoft. Web/certifikáty/* | Vytváření a Správa certifikátů webu |
> | Microsoft. Web/listSitesAssignedToHostName/Read | Získá názvy webů přiřazených k názvu hostitele. |
> | Microsoft. Web/serverových farem/JOIN/Action |  |
> | Microsoft. Web/serverových farem/Read | Získat vlastnosti v plánu App Service |
> | Microsoft. Web/weby/* | Vytváření a Správa webů (vytváření webů vyžaduje taky oprávnění k zápisu do přidruženého plánu App Service) |
> | **NotActions** |  |
> | *nTato* |  |
> | **Akce dataactions** |  |
> | *nTato* |  |
> | **NotDataActions** |  |
> | *nTato* |  |

## <a name="next-steps"></a>Další kroky

- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Správa přístupu k prostředkům Azure pomocí RBAC a webu Azure Portal](role-assignments-portal.md)
- [Oprávnění ve službě Azure Security Center](../security-center/security-center-permissions.md)
