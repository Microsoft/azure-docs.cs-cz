---
title: Spravované konektory pro Azure Logic Apps
description: Pomocí triggerů a akcí spravovaných Microsoftem můžete vytvářet automatizované pracovní postupy, které integrují jiné aplikace, data, služby a systémy pomocí Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: d50cf1009fb674f2d3d5914715dcbd9699565489
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796727"
---
# <a name="managed-connectors-for-logic-apps"></a>Spravované konektory pro Logic Apps

[Spravované konektory](apis-list.md) poskytují možnosti přístupu k dalším službám a systémům, kde nejsou k dispozici [integrované aktivační události a akce](built-in.md) . Tyto triggery a akce můžete použít k vytvoření pracovních postupů, které integrují data, aplikace, cloudové služby a místní systémy. V porovnání s vestavěnými aktivačními událostmi a akcemi jsou tyto konektory obvykle vázané na určitou službu nebo systém, jako je Azure Blob Storage, Office 365, SQL, Salesforce nebo SFTP. Spravované konektory spravované Microsoftem a hostované v Azure obvykle vyžadují, abyste nejdřív vytvořili připojení z pracovního postupu a ověřili vaši identitu. K dispozici jsou triggery založené na opakování i na webhookech, takže pokud použijete Trigger založený na opakování, přečtěte si [Přehled chování opakování](apis-list.md#recurrence-behavior).

Pro několik služeb, systémy a protokoly, například Azure Service Bus, Azure Functions, SQL, AS2 a tak dále, poskytují Logic Apps také integrované verze. Počet a rozsah se liší v závislosti na tom, zda vytvoříte aplikaci logiky pro více tenantů nebo aplikaci logiky pro jednoho tenanta. V několika případech jsou k dispozici obě předdefinované verze a verze spravovaného konektoru. Ve většině případů předdefinovaná verze nabízí lepší výkon, možnosti, ceny a tak dále. Chcete-li například [vyměňovat zprávy B2B pomocí protokolu AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), vyberte vestavěnou verzi, pokud nepotřebujete sledování, které jsou k dispozici pouze v (nepoužívaném) verzi spravovaného konektoru.

Některé spravované konektory pro Logic Apps patří do několika dílčích kategorií. Konektor SAP je například konektor [Enterprise](#enterprise-connectors) i konektor [On-Premises Connector](#on-premises-connectors).

* [Standardní konektory](#standard-connectors) poskytují přístup ke službám, jako je Azure Blob Storage, Office 365, SharePoint, Salesforce, Power BI, OneDrive a mnoho dalších.
* Místní [konektory](#on-premises-connectors) poskytují přístup k místním systémům, jako je SQL Server, SharePoint Server, SAP, Oracle DB, sdílené složky a další.
* [Konektory účtu pro integraci](#integration-account-connectors) vám pomůžou transformovat a ověřovat XML, kódovat a dekódovat ploché soubory a zpracovávat zprávy B2B (Business-to-Business) pomocí protokolů AS2, EDIFACT a X12. 

## <a name="standard-connectors"></a>Standardní konektory

Azure Logic Apps poskytuje tyto oblíbené standardní konektory pro vytváření automatizovaných pracovních postupů pomocí těchto služeb a systémů. Některé standardní konektory podporují také [místní systémy](#on-premises-connectors) nebo [účty pro integraci](#integration-account-connectors).

Některé konektory Logic Apps Standard podporují [místní systémy](#on-premises-connectors) nebo [účty pro integraci](#integration-account-connectors).

:::row:::
    :::column:::
        [![Ikona spravovaného konektoru Azure Service Bus v Logic Apps][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]
        \
        \
        Spravujte asynchronní zprávy, relace a odběry témat pomocí nejčastěji používaného konektoru v Logic Apps.
    :::column-end:::
    :::column:::
        [![Ikona spravovaného konektoru SQL Server v Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
        \
        \
        Připojte se k vašemu SQL Server místně nebo Azure SQL Database v cloudu, abyste mohli spravovat záznamy, spouštět uložené procedury nebo provádět dotazy.
    :::column-end:::
    :::column:::
        [![Ikona spravovaného konektoru Azure blogu Storage v Logic Apps][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure blogu Storage**][azure-blob-storage-doc]
        \
        \
        Připojte se k účtu Azure Storage, abyste mohli vytvářet a spravovat obsah objektů BLOB.
    :::column-end:::
    :::column:::
        [![Ikona spravovaného konektoru Office 365 Outlooku v Logic Apps][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        Připojte se k pracovnímu nebo školnímu e-mailovému účtu, abyste mohli vytvářet a spravovat e-maily, úkoly, události kalendáře a schůzky, kontakty, žádosti a další.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![STFP – ikona spravovaného konektoru SSH v Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP – SSH**][sftp-ssh-doc]
        \
        \
        Připojte se k serverům SFTP, ke kterým máte přístup z internetu pomocí SSH, abyste mohli pracovat se svými soubory a složkami.
    :::column-end:::
    :::column:::
        [![Ikona spravovaného konektoru SharePointu Online v Logic Apps][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        Připojte se k SharePointu Online, abyste mohli spravovat soubory, přílohy, složky atd.
    :::column-end:::
    :::column:::
        [![Ikona spravovaného konektoru front Azure v Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Fronty Azure**][azure-queues-doc]
        \
        \
        Připojte se k účtu Azure Storage, abyste mohli vytvářet a spravovat fronty a zprávy.
    :::column-end:::
    :::column:::
        [![Ikona spravovaného konektoru FTP v Logic Apps][ftp-icon]][ftp-doc]
        \
        \
        [**Adresa**][ftp-doc]
        \
        \
        Připojte se k serverům FTP, ke kterým máte přístup z Internetu, abyste mohli pracovat se soubory a složkami.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona spravovaného konektoru systému souborů v Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Systém souborů**][file-system-doc]
        \
        \
        Připojte se k místní sdílené složce, abyste mohli vytvářet a spravovat soubory.
    :::column-end:::
    :::column:::
        [![Ikona spravovaného konektoru Azure Event Hubs v Logic Apps][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Event Hubs Azure**][azure-event-hubs-doc]
        \
        \
        Zpracovávejte a publikujte události přes centrum událostí. Pomocí služby Event Hubs můžete získat výstup například z vaší aplikace logiky a pak ho odeslat poskytovateli analýz v reálném čase.
    :::column-end:::
    :::column:::
        [![Ikona spravovaného konektoru Azure Event Grid v Logic Apps][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid**][azure-event-grid-doc]
        \
        \
        Monitorujte události publikované Event Grid například při změně prostředků Azure nebo prostředků třetích stran.
    :::column-end:::
    :::column:::
        [![Ikona spravovaného konektoru Salesforce v Logic Apps][salesforce-icon]][salesforce-doc]
        \
        \
        [**Produktu**][salesforce-doc]
        \
        \
        Připojte se k účtu Salesforce, abyste mohli vytvářet a spravovat položky, jako jsou záznamy, úlohy, objekty a další.
    :::column-end:::
:::row-end:::


## <a name="on-premises-connectors"></a>Místní konektory

Než budete moct vytvořit připojení k místnímu systému, musíte nejdřív [Stáhnout, nainstalovat a nastavit místní bránu dat][gateway-doc]. Tato brána poskytuje zabezpečený komunikační kanál bez nutnosti nastavovat nezbytnou síťovou infrastrukturu. 

Následující konektory jsou některé běžně používané [standardní konektory](#standard-connectors) , které Logic Apps poskytují přístup k datům a prostředkům v místních systémech. Seznam místních konektorů najdete v tématu [podporované zdroje dat](../logic-apps/logic-apps-gateway-connection.md#supported-connections).

:::row:::
    :::column:::
        [![Ikona konektoru On-Premises Connector BizTalk serveru v Logic Apps][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru místního systému souborů v Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Systém souborů**][file-system-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru IBM Db2 On-Premises Connector v Logic Apps][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM Db2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru IBM Informix On-Premises Connector v Logic Apps][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona konektoru pro místní připojení MySQL v Logic Apps][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru Oracle DB On-Premises Connector v Logic Apps][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru PostgreSQL On-Premises Connector v Logic Apps][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru On-Premises Connector serveru SharePointu v Logic Apps][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint Designer**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona konektoru SQL Server On-Premises Connector v Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Ikona místního konektoru Teradata v Logic Apps][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-connectors"></a>Konektory účtu pro integraci

Konektory účtu pro integraci konkrétně podporují [scénáře komunikace B2B (Business-to-Business)](../logic-apps/logic-apps-enterprise-integration-overview.md) v Azure Logic Apps. Po [Vytvoření účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a definování artefaktů B2B, jako jsou obchodní partneři, smlouvy, mapy a schémata, můžete použít konektory účtu pro integraci ke kódování a dekódování zpráv, transformaci obsahu a dalších.

Pokud například používáte Microsoft BizTalk Server, můžete vytvořit připojení z pracovního postupu pomocí [konektoru BizTalk Server On-Premises Connector](#on-premises-connectors). Pomocí těchto konektorů účtu pro integraci pak můžete v pracovním postupu roztáhnout nebo provádět operace typu BizTalk.

> [!NOTE]
> Než budete moct používat konektory účtu pro integraci, musíte [propojit aplikaci logiky s účtem pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).


:::row:::
    :::column:::
        [![Ikona akce dekódování AS2 v Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**Dekódování AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Ikona akce kódování AS2 v Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**Kódování AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Ikona akce dekódování EDIFACT v Logic Apps][edifact-icon]][edifact-decode-doc]
        \
        \
        [**Dekódování EDIFACT**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![Ikona akce kódování EDIFACT v Logic Apps][edifact-icon]][edifact-encode-doc]
        \
        \
        [**Kódování EDIFACT**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona akce dekódování plochého souboru v Logic Apps][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**Dekódování plochých souborů**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Ikona akce kódování plochého souboru v Logic Apps][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**Kódování plochého souboru**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![Ikona akce účtu pro integraci v Logic Apps][integration-account-icon]][integration-account-doc]
        \
        \
        [**Účet pro integraci**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Ikona akce transformace kapalin v Logic Apps][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Transformace kapalin**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona akce dekódování X12 v Logic Apps][x12-icon]][x12-decode-doc]
        \
        \
        [**Dekódování X12**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![Ikona akce kódování X12 v Logic Apps][x12-icon]][x12-encode-doc]
        \
        \
        [**Kódování X12**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![Ikona akce transformačního souboru XML v Logic Apps][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**Transformace XML**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![Ikona akce ověření XML v Logic Apps][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**Ověření XML**][xml-validate-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>Podnikové konektory

Následující konektory poskytují přístup k podnikovým systémům za další náklady:

:::row:::
    :::column:::
        [![Ikona konektoru IBM 3270 Enterprise v Logic Apps][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [Konektor **IBM 3270** Enterprise][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru IBM MQ Enterprise v Logic Apps][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [Konektor **IBM MQ** Enterprise][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Ikona SAP Enterprise Connectoru v Logic Apps][sap-icon]][sap-connector-doc]
        \
        \
        [Konektor **SAP** Enterprise][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::


## <a name="ise-connectors"></a>Konektory ISE

V prostředí ISE (Integration Service Environment) mají tyto spravované konektory také [verze ISE](apis-list.md#ise-and-connectors), které mají různé možnosti než jejich víceklientské verze:

> [!NOTE]
> Logic Apps, které běží na ISE a jejich konektory, bez ohledu na to, kde se tyto konektory spouštějí, sledují pevný Cenový tarif oproti cenovému plánu založenému na spotřebě. Další informace najdete v tématu [Logic Apps cenového modelu](../logic-apps/logic-apps-pricing.md) a [podrobnosti o cenách Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

:::row:::
    :::column:::
        [![Ikona konektoru ISE AS2 v Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**AS2** ISE][as2-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE Azure Automation v Logic Apps][azure-automation-icon]][azure-automation-doc]
        \
        \
        [**Azure Automation** ISE][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru služby Azure Blob Storage ISE v Logic Apps][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**BLOB Storage Azure** ISE][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE Azure Cosmos DB v Logic Apps][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB** ISE][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona konektoru služby Azure Event Hubs ISE v Logic Apps][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Event Hubs Azure** ISE][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE Azure Event Grid v Logic Apps][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid** ISE][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru služby Azure File Storage ISE v Logic Apps][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**File Storage Azure** ISE][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE Azure Key Vault v Logic Apps][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [**Azure Key Vault** ISE][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona konektoru ISE protokolu Azure Monitor v Logic Apps][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [**Protokoly Azure monitor** ISE][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE Azure Service Bus v Logic Apps][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus** ISE][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE služby Azure synapse Analytics v Logic Apps][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [**Azure synapse Analytics** ISE][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru služby Azure Table Storage ISE v Logic Apps][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [**Table Storage Azure** ISE][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona konektoru služby Azure Queues ISE v Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Fronty Azure** ISE][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE EDIFACT v Logic Apps][edifact-icon]][edifact-doc]
        \
        \
        [**EDIFACT** ISE][edifact-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE systému souborů v Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Systém souborů** ISE][file-system-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE serveru FTP v Logic Apps][ftp-icon]][ftp-doc]
        \
        \
        [**Server FTP** ISE][ftp-doc]
    :::column-end:::
:::row-end:::   
:::row:::
    :::column:::
        [![Ikona konektoru IBM 3270 ISE v Logic Apps][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** ISE][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru IBM DB2 ISE v Logic Apps][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2** ISE][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru IBM MQ ISE v Logic Apps][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** ISE][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru SAP ISE v Logic Apps][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** ISE][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SFTP – ikona konektoru SSH ISE v Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**SFTP – SSH** ISE][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE SMTP v Logic Apps][smtp-icon]][smtp-doc]
        \
        \
        [**Protokol SMTP** ISE][smtp-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE SQL Server v Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server** ISE][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Ikona konektoru ISE X12 v Logic Apps][x12-icon]][x12-doc]
        \
        \
        [**X12** ISE][x12-doc]
    :::column-end:::
:::row-end:::

Další informace najdete v těchto tématech:

* [Přístup k prostředkům virtuální sítě Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Cenový model Logic Apps](../logic-apps/logic-apps-pricing.md)
* [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytváření vlastních rozhraní API, která můžete volat z Logic Apps](/logic-apps/logic-apps-create-api-app)

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png


<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Vytváření a správa úloh automatizace pro cloudovou a místní infrastrukturu"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Správa souborů v kontejneru objektů blob pomocí konektoru služby Blob Storage"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Připojte se k Azure Cosmos DB, abyste mohli přistupovat k dokumentům a uloženým procedurám."
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorování událostí publikovaných Event Grid, například při změně prostředků Azure nebo prostředků třetích stran"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Připojte se k Azure Event Hubs, abyste mohli přijímat a odesílat události mezi Logic Apps a Event Hubs"
[azure-file-storage-doc]: /connectors/azurefile/ "Připojte se k účtu Azure Storage, abyste mohli vytvářet, aktualizovat, získávat a odstraňovat soubory."
[azure-key-vault-doc]: /connectors/keyvault/ "Připojte se k vašemu Azure Key Vault, abyste mohli spravovat tajná klíče a klíče."
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Spouštění dotazů na protokoly Azure Monitor napříč Log Analyticsmi pracovními prostory a Application Insights komponentami"
[azure-queues-doc]: /connectors/azurequeues/ "Připojte se k účtu Azure Storage, abyste mohli vytvářet a spravovat fronty a zprávy."
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Odesílání zpráv z Service Bus front a témat a příjem zpráv z Service Busch front a předplatných"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Připojte se ke službě Azure synapse Analytics, abyste mohli zobrazit data."
[azure-table-storage-doc]: /connectors/azuretables/ "Připojte se k účtu Azure Storage, abyste mohli vytvářet, aktualizovat a dotazovat tabulky a další."
[biztalk-server-doc]: /connectors/biztalk/ "Připojte se k vašemu BizTalk Server, abyste mohli spouštět aplikace založené na BizTalku souběžně s Azure Logic Apps"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Připojení k místnímu systému souborů"
[ftp-doc]: ./connectors-create-api-ftp.md "Připojení k serverům FTP/FTPS a provádění úloh protokolu FTP, jako je odesílání, získávání a odstraňování souborů a provádění dalších akcí"
[github-doc]: ./connectors-create-api-github.md "Připojení ke GitHubu a sledování problémů"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Připojí se ke kalendáři Google a může spravovat kalendář."
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Připojte se k tabulkám Google, abyste mohli upravovat své listy."
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Připojí se k úkolům Google, abyste mohli spravovat své úkoly."
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Připojení k aplikacím 3270 na sálových počítačích IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Připojte se k IBM DB2 v cloudu nebo místně. Aktualizace řádku, získání tabulky a dalších"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Připojte se k Informix v cloudu nebo místně. Čtení řádku, seznam tabulek a další"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Připojení k IBM MQ v místním prostředí nebo v Azure pro odesílání a příjem zpráv"
[instagram-doc]: ./connectors-create-api-instagram.md "Připojte se k Instagramu. Trigger nebo činnost na událostech"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Připojení k Mandrillu pro komunikaci"
[mysql-doc]: /connectors/mysql/ "Připojte se k místní databázi MySQL, abyste mohli číst a zapisovat data."
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Připojte se k pracovnímu nebo školnímu účtu, abyste mohli odesílat a přijímat e-maily, spravovat kalendář a kontakty a využívat další možnosti."
[onedrive-doc]: ./connectors-create-api-onedrive.md "Připojte se k osobnímu Microsoft OneDrivu, abyste mohli nahrávat, odstraňovat, vypisovat soubory a další."
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Připojte se k firemnímu Microsoft OneDrive, abyste mohli nahrávat, odstraňovat, vypisovat soubory a další."
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Připojte se k databázi Oracle, abyste mohli přidávat, vkládat, odstraňovat řádky a další."
[outlook.com-doc]: ./connectors-create-api-outlook.md "Připojte se k poštovní schránce Outlooku, abyste mohli spravovat své e-maily, kalendáře, kontakty a další funkce."
[postgre-sql-doc]: /connectors/postgresql/ "Připojte se k databázi PostgreSQL, abyste mohli číst data z tabulek."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Připojte se k účtu Salesforce. Správa účtů, zájemců, příležitostí a dalších"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Připojení k místnímu systému SAP"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Připojte se k SendGrid. Odesílání e-mailů a Správa seznamů příjemců"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Připojte se k účtu SFTP pomocí SSH. Nahrávání, získávání, odstraňování souborů a další"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Připojte se k místnímu serveru SharePoint. Správa dokumentů, položek seznamů a dalších"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Připojte se k SharePointu Online. Správa dokumentů, položek seznamů a dalších"
[slack-doc]: ./connectors-create-api-slack.md "Připojení k časové rezervě a odeslání zpráv do kanálů časové rezervy"
[smtp-doc]: ./connectors-create-api-smtp.md "Připojení k serveru SMTP a odesílání e-mailů s přílohami"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Připojení ke SparkPostu pro komunikaci"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Připojení k Azure SQL Database nebo SQL Server. Vytvoření, aktualizace, získání a odstranění záznamů v tabulce databáze SQL"
[teradata-doc]: /connectors/teradata/ "Připojení k databázi Teradata pro čtení dat z tabulek"
[twilio-doc]: ./connectors-create-api-twilio.md "Připojte se k Twilio. Posílání a získávání zpráv, získávání dostupných čísel, Správa příchozích telefonních čísel a další"
[youtube-doc]: ./connectors-create-api-youtube.md "Připojte se k YouTube. Správa videí a kanálů"

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Kódování a dekódování zpráv, které používají protokol AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Kódování a dekódování zpráv, které používají protokol EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Dekódovat zprávy používající protokol EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Kódování zpráv, které používají protokol EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Seznamte se s plochým souborem Enterprise Integration"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Seznamte se s plochým souborem Enterprise Integration"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Správa metadat pro artefakty účtu integrace"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transformace JSON pomocí šablon kapalin"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Kódování a dekódování zpráv, které používají protokol X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Dekódovat zprávy používající protokol X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Kódování zpráv, které používají protokol X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transformovat zprávy XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Ověřit zprávy XML"


<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Připojení k místním zdrojům dat z aplikací logiky pomocí místní brány dat"



<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Kódování a dekódování zpráv, které používají protokol AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Kódování a dekódování zpráv, které používají protokol EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Dekódovat zprávy používající protokol EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Kódování zpráv, které používají protokol EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Seznamte se s plochým souborem Enterprise Integration"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Seznamte se s plochým souborem Enterprise Integration"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Správa metadat pro artefakty účtu integrace"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transformace JSON pomocí šablon kapalin"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Kódování a dekódování zpráv, které používají protokol X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Dekódovat zprávy používající protokol X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Kódování zpráv, které používají protokol X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transformovat zprávy XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Ověřit zprávy XML"
