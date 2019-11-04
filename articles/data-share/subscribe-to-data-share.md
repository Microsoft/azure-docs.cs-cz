---
title: 'Kurz: přijetí dat pro příjem & – Azure Data Share'
description: Kurz – přijetí a příjem dat pomocí Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 9c24f54fe846459187488b0a65b2582914e25e2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499351"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Kurz: přijetí a příjem dat pomocí Azure Data Share  

V tomto kurzu se dozvíte, jak přijmout pozvánku ke sdílení dat pomocí Azure Data Share. Naučíte se, jak přijímat data, která s vámi sdílíte, a jak povolit pravidelný interval aktualizace, abyste měli jistotu, že budete mít vždycky nejnovější snímek dat, která s vámi někdo sdílí. 

> [!div class="checklist"]
> * Jak přijmout pozvánku ke sdílené složce Azure Data
> * Vytvoření účtu Azure Data Share
> * Zadejte cíl pro vaše data.
> * Vytvořte předplatné pro vaši sdílenou složku dat pro plánovanou aktualizaci.

## <a name="prerequisites"></a>Předpoklady
Než budete moct přijmout pozvánku ke sdílení dat, musíte zřídit několik prostředků Azure, které jsou uvedené níže. 

Před přijetím pozvánky ke sdílení dat se ujistěte, že jsou splněné všechny požadavky. 

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Pozvánka ke sdílení dat: Pozvánka od Microsoft Azure k předmětu s názvem "pozvání ke sdílené složce Azure z **<yourdataprovider@domain.com>** ".

### <a name="receive-data-into-a-storage-account"></a>Příjem dat do účtu úložiště: 

* Účet Azure Storage: Pokud ho ještě nemáte, můžete vytvořit [účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici ve *službě Microsoft. Authorization/přiřazení role/* oprávnění k zápisu. Toto oprávnění existuje v roli vlastníka. 
* Registrace poskytovatele prostředků pro sdílenou složku Microsoft. datashare. Informace o tom, jak to udělat, najdete v dokumentaci pro [poskytovatele prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) . 

> [!IMPORTANT]
> Abyste mohli přijmout a přijmout sdílenou složku Azure, musíte nejdřív zaregistrovat poskytovatele prostředků Microsoft. datashare a musíte být vlastníkem účtu úložiště, do kterého přijímáte data. Postupujte podle pokynů popsaných v tématu [řešení potíží se službou Azure Data Share](data-share-troubleshoot.md) a zaregistrujte poskytovatele prostředků sdílení dat a přidejte sami sebe jako vlastníka účtu úložiště. 

### <a name="receive-data-into-a-sql-based-source"></a>Příjem dat do zdroje založeného na jazyce SQL:

* Oprávnění ke sdílení dat MSI pro přístup k Azure SQL Database nebo Azure SQL Data Warehouse. To lze provést pomocí následujících kroků: 
    1. Nastavte jako správce Azure Active Directory pro server.
    1. Připojte se k Azure SQL Database/datový sklad pomocí Azure Active Directory.
    1. Pomocí Editoru dotazů (Preview) spusťte následující skript, který přidá soubor MSI pro sdílení dat jako db_owner. Je nutné se připojit pomocí služby Active Directory a SQL Server ověřování. 

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Všimněte si, že *< share_acc_name >* je název vašeho účtu pro sdílení dat. Pokud jste ještě nevytvořili účet pro sdílení dat, můžete se k tomuto předběžnému požadavku vrátit později.         

* IP adresa klienta SQL Server přístup k bráně firewall: dá se to udělat pomocí následujících kroků: 1. Přejděte na *brány firewall a virtuální sítě* 1. Kliknutím na přepínač **zapnuto** povolíte přístup ke službám Azure. 

Po dokončení těchto požadavků budete připraveni přijímat data do svého SQL Server.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Otevřít pozvánku

Prohlédněte si doručenou poštu pro pozvání od poskytovatele dat. Pozvánka pochází z Microsoft Azure s názvem **pozvání Azure Data Share z <yourdataprovider@domain.com>** . Poznamenejte si název sdílené složky, abyste se ujistili, že přijímáte správnou sdílenou složku v případě, že existuje více pozvání. 

Výběrem **Zobrazit Pozvánka** zobrazíte pozvánku v Azure. Tím přejdete do zobrazení přijaté sdílené složky.

![Zasílán](./media/invitations.png "Seznam pozvánek") 

Vyberte sdílenou složku, kterou chcete zobrazit. 

## <a name="accept-invitation"></a>Přijmout pozvánku
Ujistěte se, že všechna pole jsou přezkoumána, včetně **podmínek použití**. Pokud souhlasíte s podmínkami použití, budete muset zaškrtnout políčko, abyste označili, že souhlasíte. 

![Podmínky použití](./media/terms-of-use.png "Podmínky použití") 

V části *cílový účet sdílení dat*vyberte předplatné a skupinu prostředků, do které budete nasazovat sdílenou složku. 

V poli **účet pro sdílení dat** vyberte **vytvořit novou** , pokud nemáte existující účet pro sdílení dat. V opačném případě vyberte existující účet pro sdílení dat, do kterého chcete vaši sdílenou složku přijmout. 

V poli *přijatý název sdílené složky* můžete ponechat výchozí hodnotu zadanou daty nebo zadat nový název přijaté sdílené složky. 

![Cílový účet pro sdílení dat](./media/target-data-share.png "Cílový účet pro sdílení dat") 

Až budete souhlasit s podmínkami použití a zadáte umístění pro sdílenou složku, vyberte možnost *přijmout a konfigurovat*. Pokud jste vybrali tuto možnost, vytvoří se předplatné sdílení a další obrazovka vás vyzve k výběru cílového účtu úložiště, do kterého se mají data zkopírovat. 

![Přijmout možnosti](./media/accept-options.png "Přijmout možnosti") 

Pokud chcete pozvánku přijmout teď, ale později nakonfigurovat úložiště, vyberte *přijmout a nakonfigurovat později*. Tato možnost umožňuje později nakonfigurovat cílový účet úložiště. Pokud chcete později pokračovat v konfiguraci úložiště, přečtěte si téma [Postup konfigurace účtu úložiště](how-to-configure-mapping.md) , kde najdete podrobné pokyny pro obnovení konfigurace sdílené složky. 

Pokud nechcete pozvánku přijmout, vyberte *odmítnout*. 

## <a name="configure-storage"></a>Konfigurace úložiště
V části *nastavení cílového úložiště*vyberte předplatné, skupinu prostředků a účet úložiště, do kterého chcete data přijímat. 

![Nastavení cílového úložiště](./media/target-storage-settings.png "Cílové úložiště") 

Pokud chcete dostávat běžná aktualizace dat, ujistěte se, že jste povolili nastavení snímků. Všimněte si, že se zobrazí jenom plán nastavení snímků, pokud ho poskytovatel dat zahrnul do sdílené složky dat. 

![Nastavení snímku](./media/snapshot-settings.png "Nastavení snímku") 

Vyberte *Save* (Uložit). 

> [!IMPORTANT]
> Pokud přijímáte data založená na jazyce SQL a chcete tato data přijímat do zdroje založeného na jazyce SQL, přečtěte si náš průvodce [konfigurací mapování datových sad](how-to-configure-mapping.md) , kde se dozvíte, jak nakonfigurovat SQL Server jako cíl pro datovou sadu. 

## <a name="trigger-a-snapshot"></a>Aktivace snímku

Snímek můžete aktivovat na kartě přijaté sdílené složky – > Podrobnosti výběrem **spouštěcího snímku**. Tady můžete aktivovat úplný nebo přírůstkový snímek dat. Pokud data od poskytovatele dat přijímáte poprvé, vyberte možnost úplné kopírování. 

![Spustit snímek](./media/trigger-snapshot.png "Spustit snímek") 

Po *úspěšném*stavu posledního spuštění otevřete účet úložiště a zobrazte přijatá data. 

Pokud chcete zjistit, který účet úložiště jste použili, vyberte v **datových sadách**. 

![Datové sady příjemců](./media/consumer-datasets.png "Mapování datové sady příjemce") 

## <a name="view-history"></a>Zobrazení historie
Pokud chcete zobrazit historii snímků, přejděte k části přijaté sdílené složky – Historie >. Tady najdete historii všech snímků vygenerovaných za posledních 60 dnů. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu se naučíte, jak přijmout a získat sdílenou složku Azure Data. Další informace o konceptech Azure Data Share najdete v článku [Koncepty: terminologie Azure Data Share](terminology.md).