---
title: Customer Lockbox pro Microsoft Azure
description: Technický přehled Customer Lockbox pro Microsoft Azure, který poskytuje kontrolu nad přístupem od poskytovatele cloudu, když Microsoft může potřebovat přístup k zákaznickým datům.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 09/15/2020
ms.openlocfilehash: 52cb5ac5423aac0599ba2827667ee670dde286a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331654"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Customer Lockbox pro Microsoft Azure

> [!NOTE]
> Aby mohla vaše organizace používat tuto funkci, musí mít [plán podpory Azure](https://azure.microsoft.com/support/plans/) s minimální úrovní **vývojářů**.

Customer Lockbox pro Microsoft Azure poskytuje rozhraní pro zákazníky, ve kterém můžou kontrolovat a schvalovat nebo zamítat žádosti zákazníků o přístup k datům. Používá se v případech, kdy technici Microsoftu potřebují získat přístup k datům zákazníků během zpracování žádostí o podporu.

Tento článek popisuje, jak se iniciují a ukládají požadavky na Customer Lockbox pro pozdější revize a audity.

Customer Lockbox je teď obecně dostupný a v současné době podporuje přístup ke vzdálené ploše virtuálních počítačů.

## <a name="supported-services-and-scenarios-in-preview"></a>Podporované služby a scénáře ve verzi Preview

Následující služby pro Customer Lockbox jsou v současné době ve verzi Preview:

- API Management
- Azure App Service
- Cognitive Services
- Container Registry
- Azure Database for MySQL
- Azure Databricks
- Azure Data Box
- Průzkumník dat Azure
- Azure Data Factory
- Azure Database for PostgreSQL
- Azure Functions
- HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Azure Storage
- Azure SQL DB
- Přenosy předplatných Azure
- Azure Synapse Analytics
- Virtuální počítače (nově popisuje také přístup k výpisům paměti a spravovaným diskům)

Pokud chcete povolit Customer Lockbox pro tyto nabídky verze Preview pro vaši organizaci, zaregistrujte se do [Customer Lockbox pro Azure Public Preview](https://aka.ms/customerlockbox/insiderprogram).

## <a name="supported-services-and-scenarios-in-general-availability"></a>Podporované služby a scénáře ve všeobecné dostupnosti

Následující služby a scénáře jsou aktuálně všeobecně dostupné pro Customer Lockbox.

### <a name="remote-desktop-access-to-virtual-machines"></a>Přístup ke vzdálené ploše virtuálních počítačů

Customer Lockbox v současné době podporuje žádosti o přístup ke vzdálené ploše virtuálních počítačů. Podporují se následující úlohy:
- Platforma jako služba (PaaS) – Azure Cloud Services (webová role a role pracovního procesu)
- Infrastruktura jako služba (IaaS) – Windows a Linux (pouze Azure Resource Manager)
- Škálovací sada virtuálních počítačů – Windows a Linux

> [!NOTE]
> IaaS klasické instance nejsou podporovány Customer Lockbox. Pokud máte úlohy spuštěné v IaaS klasických instancích, doporučujeme, abyste je migrovali z modelu nasazení Classic do Správce prostředků. Pokyny najdete v tématu [Platformou podporovaná migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Podrobné protokoly auditu

V případě scénářů, které zahrnují přístup ke vzdálené ploše, můžete s využitím protokolů událostí Windows zkontrolovat akce provedené technikem podpory. Zvažte použití služby Azure Security Center ke shromáždění protokolů událostí a zkopírování dat do vašeho pracovního prostoru pro účely analýzy. Další informace najdete v tématu [Shromažďování dat ve službě Azure Security Center](../../security-center/security-center-enable-data-collection.md).

## <a name="workflow"></a>Pracovní postup

Následující kroky popisují typický pracovní postup pro Customer Lockbox požadavek.

1. Někdo v organizaci má problém s jeho úlohou Azure.

2. Až tato osoba problém vyřeší, ale nemůže ji opravit, otevře lístek podpory z [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Lístek se přiřadí pracovníkovi podpory pro zákazníky Azure.

3. Pracovník podpory Azure zkontroluje požadavek služby a určí další kroky k vyřešení tohoto problému.

4. Pokud pracovník podpory nemůže vyřešit problém pomocí standardních nástrojů a telemetrie, je dalším krokem vyžádání zvýšených oprávnění pomocí služby pro přístup JIT (just-in-time). Tento požadavek může být od původního inženýra podpory. Nebo může to být z jiného inženýra, protože problém se překládá týmu Azure DevOps.

5. Po odeslání žádosti o přístup prostřednictvím inženýra Azure vyhodnotí služba za běhu požadavek, který vezme v úvahu následující faktory:
    - Rozsah prostředku
    - Zda žadatel představuje izolovanou identitu nebo používá službu Multi-Factor Authentication
    - Úrovně oprávnění

    Na základě pravidla JIT může tento požadavek zahrnovat taky schválení interních schvalovatelů Microsoftu. Schvalovatelem může být například vedoucí zákaznická podpora nebo DevOps Manager.

6. Když požadavek vyžaduje přímý přístup k zákaznickým datům, iniciuje se Customer Lockbox požadavek. Například přístup ke vzdálené ploše pro virtuální počítač zákazníka.

    Požadavek je nyní ve stavu **oznámeno zákazníkovi** a čeká na schválení zákazníka před udělením přístupu.

7. V organizaci zákazníka obdrží uživatel, který má [roli vlastníka](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) pro předplatné Azure, E-mail od Microsoftu a upozorní je na nedokončený požadavek na přístup. U žádostí o Customer Lockbox je tato osoba určeným schvalovatelem.

    Příklad e-mailu:

    ![Azure Customer Lockbox – oznámení e-mailem](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. E-mailové oznámení poskytuje odkaz na okno **Customer Lockbox** v Azure Portal. Pomocí tohoto odkazu se určený schvalovatel přihlásí k Azure Portal, aby se zobrazily nedokončené žádosti, jejichž organizace má Customer Lockbox:

    ![Azure Customer Lockbox – cílová stránka](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   Požadavek zůstane ve frontě Customer po dobu čtyř dnů. Po této době platnost žádosti o přístup automaticky vyprší a neudělí se jim žádný přístup pro inženýry Microsoftu.

9. Chcete-li získat podrobnosti o žádosti, která čeká na vyřízení, může vybraný schvalovatel vybrat požadavek bezpečnostního modulu z **nevyřízených požadavků**:

    ![Azure Customer Lockbox – zobrazení žádosti, která čeká na vyřízení](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. Určený schvalovatel taky může vybrat **ID žádosti o službu** a zobrazit žádost o lístek podpory, kterou vytvořil původní uživatel. Tyto informace poskytují kontext pro důvody, proč se zabývají podpora Microsoftu, a historii nahlášeného problému. Například:

    ![Azure Customer Lockbox – zobrazení žádosti o lístek podpory](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Po zkontrolování žádosti vybraný schvalovatel vybere **schválit** nebo **Odepřít**:

    ![Azure Customer Lockbox – vyberte schválit nebo zamítnout.](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    V důsledku výběru:
    - **Schválit**: přístup k Microsoft inženýru je udělený. Přístup se udělí pro výchozí období osmi hodin.
    - **Zamítnutí**: žádost o zvýšený přístup od Microsoft inženýra je odmítnutá a není provedena žádná další akce.

Pro účely auditování jsou akce prováděné v tomto pracovním postupu přihlášeny [Customer Lockbox protokoly žádostí](#auditing-logs).

## <a name="auditing-logs"></a>Protokoly auditování

Protokoly Customer Lockboxu se ukládají v protokolech aktivit. V Azure Portal vyberte **protokoly aktivit** a zobrazte informace o auditování týkající se Customer Lockbox požadavků. Můžete vyfiltrovat konkrétní akce, jako například:
- **Zamítnutí požadavku Lockboxu**
- **Vytvoření požadavku Lockboxu**
- **Schválení požadavku Lockboxu**
- **Vypršení platnosti žádosti bezpečnostního modulu**

Příklad:

![Azure Customer Lockbox – protokoly aktivit](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Integrace Customer Lockboxu se srovnávacím testem zabezpečení Azure

Představili jsme nový základní ovládací prvek ([3,13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) v rámci srovnávacího testu zabezpečení Azure, který pokrývá Customer Lockbox použitelnost. Zákazníci teď můžou využít srovnávací testy ke kontrole Customer Lockbox použitelnosti služby.

## <a name="exclusions"></a>Vyloučení

Požadavky Customer Lockboxu se neaktivují v následujících scénářích technické podpory:

- Technik Microsoftu potřebuje provést aktivitu, která nepatří mezi standardní provozní postupy. Příkladem je obnovení nebo zotavení služeb v neočekávaných nebo nepředvídatelných scénářích.

- Technik Microsoftu přistupuje k platformě Azure v rámci řešení potíží a neúmyslně získá přístup k datům zákazníků. Například v důsledku řešení potíží síťovým týmem Azure může dojít k zachytávání paketů na síťovém zařízení. Pokud však zákazník přenášená data zašifroval, technik je nebude moct přečíst.

## <a name="next-steps"></a>Další kroky

Customer Lockbox je k dispozici automaticky pro všechny zákazníky, kteří mají [plán podpory Azure](https://azure.microsoft.com/support/plans/) s minimální úrovní **vývojářů**.

Pokud máte oprávněný plán podpory, nemusíte k povolení Customer Lockbox použít žádnou akci. Pokud je tato akce nutná pro průběh lístku podpory, který je popsán od někoho ve vaší organizaci, je žádost o Customer Lockbox iniciovaná pracovníkem Microsoftu.
