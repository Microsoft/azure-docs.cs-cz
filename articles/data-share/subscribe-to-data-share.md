---
title: 'Kurz: Přijmout data přijetí & – Preview Azure Data Share Preview'
description: Kurz – přijetí a příjem dat pomocí Azure Data Share Preview
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 235ef25b2d655c4388dee5bdcf88d179f3373697
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327400"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>Kurz: Přijetí a příjem dat pomocí Azure Data Share Preview

V tomto kurzu se dozvíte, jak přijmout pozvánku ke sdílení dat pomocí Azure Data Share Preview. Naučíte se, jak přijímat data, která s vámi sdílíte, a jak povolit pravidelný interval aktualizace, abyste měli jistotu, že budete mít vždycky nejnovější snímek dat, která s vámi někdo sdílí. 

> [!div class="checklist"]
> * Jak přijmout pozvánku ke službě Azure Data Share Preview
> * Vytvoření účtu verze Preview služby Azure Data Share
> * Zadejte cíl pro vaše data.
> * Vytvořte předplatné pro vaši sdílenou složku dat pro plánovanou aktualizaci.

## <a name="prerequisites"></a>Požadavky
Než budete moct přijmout pozvánku ke sdílení dat, musíte zřídit několik prostředků Azure, které jsou uvedené níže. 

Před přijetím pozvánky ke sdílení dat se ujistěte, že jsou splněné všechny požadavky. 

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Účet Azure Storage: Pokud ho ještě nemáte, můžete vytvořit [účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Pozvánka ke sdílení dat: Pozvánka z Microsoft Azure s předmětem s názvem "pozvání ke sdílení dat Azure z **<yourdataprovider@domain.com>** ".
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici ve *službě Microsoft. Authorization/přiřazení role/* oprávnění k zápisu. Toto oprávnění existuje v roli vlastníka. 
* Registrace poskytovatele prostředků pro sdílenou složku Microsoft. datashare. Informace o tom, jak to udělat, najdete v dokumentaci pro [poskytovatele prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) . 

> [!IMPORTANT]
> Abyste mohli přijmout a přijmout sdílenou složku Azure, musíte nejdřív zaregistrovat poskytovatele prostředků Microsoft. datashare a musíte být vlastníkem účtu úložiště, do kterého přijímáte data. Postupujte podle pokynů popsaných v tématu [řešení potíží s Azure Data Share Preview](data-share-troubleshoot.md) a zaregistrujte poskytovatele prostředků sdílené složky dat a přidejte sami sebe jako vlastníka účtu úložiště. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Otevřít pozvánku

Prohlédněte si doručenou poštu pro pozvání od poskytovatele dat. Pozvánka pochází z Microsoft Azure s názvem **pozvání Azure Data Share z <yourdataprovider@domain.com>** . Poznamenejte si název sdílené složky, abyste se ujistili, že přijímáte správnou sdílenou složku v případě, že existuje více pozvání. 

Výběrem **Zobrazit Pozvánka** zobrazíte pozvánku v Azure. Tím přejdete do zobrazení přijaté sdílené složky.

![](./media/invitations.png "Seznam") pozvánek 

Vyberte sdílenou složku, kterou chcete zobrazit. 

## <a name="accept-invitation"></a>Přijmout pozvánku
Ujistěte se, že všechna pole jsou přezkoumána, včetně **podmínek použití**. Pokud souhlasíte s podmínkami použití, budete muset zaškrtnout políčko, abyste označili, že souhlasíte. 

![Podmínky použití](./media/terms-of-use.png "podmínky použití") 

V části *cílový účet sdílení dat*vyberte předplatné a skupinu prostředků, do které budete nasazovat sdílenou složku. 

V poli **účet pro sdílení dat** vyberte **vytvořit novou** , pokud nemáte existující účet pro sdílení dat. V opačném případě vyberte existující účet pro sdílení dat, do kterého chcete vaši sdílenou složku přijmout. 

V poli *přijatý název sdílené složky* můžete ponechat výchozí hodnotu zadanou daty nebo zadat nový název přijaté sdílené složky. 

Účet cílové sdílené(./media/target-data-share.png "datové") složky pro ![cílovou datovou sdílenou složku] 

Až budete souhlasit s podmínkami použití a zadáte umístění pro sdílenou složku, vyberte možnost *přijmout a konfigurovat*. Pokud jste vybrali tuto možnost, vytvoří se předplatné sdílení a další obrazovka vás vyzve k výběru cílového účtu úložiště, do kterého se mají data zkopírovat. 

Možnosti ![přijetí možností](./media/accept-options.png "přijetí") 

Pokud chcete pozvánku přijmout teď, ale později nakonfigurovat úložiště, vyberte *přijmout a nakonfigurovat později*. Tato možnost umožňuje později nakonfigurovat cílový účet úložiště. Pokud chcete později pokračovat v konfiguraci úložiště, přečtěte si téma [Postup konfigurace účtu úložiště](how-to-configure-mapping.md) , kde najdete podrobné pokyny pro obnovení konfigurace sdílené složky. 

Pokud nechcete pozvánku přijmout, vyberte *odmítnout*. 

## <a name="configure-storage"></a>Konfigurace úložiště
V části *nastavení cílového úložiště*vyberte předplatné, skupinu prostředků a účet úložiště, do kterého chcete data přijímat. 

(./media/target-storage-settings.png "Cílové úložiště") ![nastavení cílového úložiště] 

Pokud chcete dostávat běžná aktualizace dat, ujistěte se, že jste povolili nastavení snímků. Všimněte si, že se zobrazí jenom plán nastavení snímků, pokud ho poskytovatel dat zahrnul do sdílené složky dat. 

(./media/snapshot-settings.png "Nastavení snímku") ![Nastavení snímků] 

Vyberte *Uložit*. 

## <a name="trigger-a-snapshot"></a>Aktivace snímku

Snímek můžete aktivovat na kartě přijaté sdílené složky – > Podrobnosti výběrem **spouštěcího snímku**. Tady můžete aktivovat úplný nebo přírůstkový snímek dat. Pokud data od poskytovatele dat přijímáte poprvé, vyberte možnost úplné kopírování. 

![Spustit](./media/trigger-snapshot.png "snímek triggeru") snímku 

Po *úspěšném*stavu posledního spuštění otevřete účet úložiště a zobrazte přijatá data. 

Pokud chcete zjistit, který účet úložiště jste použili, vyberte v **datových sadách**. 

(./media/consumer-datasets.png "Mapování datové sady příjemce") ![datových sad příjemce] 

## <a name="view-history"></a>Zobrazení historie
Pokud chcete zobrazit historii snímků, přejděte k části přijaté sdílené složky – Historie >. Tady najdete historii všech snímků vygenerovaných za posledních 60 dnů. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu se naučíte, jak přijmout a získat sdílenou složku Azure Data. Pokud se chcete dozvědět víc o konceptech Azure Data Share, pokračujte @no__t – 0Concepts: Terminologie Azure Data Shares @ no__t-0.