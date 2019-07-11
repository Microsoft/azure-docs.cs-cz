---
title: Kurz – přijmout a přijímat data pomocí Azure Data sdílené složky ve verzi Preview
description: Kurz – přijmout a přijímat data pomocí Azure Data sdílené složky ve verzi Preview
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 2dc4994d88fc03c23a6d5722d6018c926e7d6b8c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788161"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>Kurz: Přijmout a přijímat data pomocí Azure Data sdílené složky ve verzi Preview

V tomto kurzu se dozvíte, jak přijmout pozvánku sdílené složky data pomocí Azure Data sdílet Preview. Dozvíte se, jak přijmout data jsou s vámi sdílí, jakož i jak povolit interval pravidelné aktualizace vždy měli nejnovější snímek dat se s vámi nesdílí. 

> [!div class="checklist"]
> * Jak přijmout pozvánku Azure dat sdílené složky ve verzi Preview
> * Vytvoření účtu služby Azure Data sdílené složky ve verzi Preview
> * Určete cíl pro vaše data
> * Vytvoření odběru pro svou sdílenou složku dat pro plánovanou aktualizaci

## <a name="prerequisites"></a>Požadavky
Předtím, než může přijmout pozvánku sdílení dat, je třeba zřídit několik prostředků Azure, které jsou uvedeny níže. 

Zajistěte, aby byly všechny požadavky kompletní před přijímá se Pozvánka sdílenou složku data. 

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Účet služby Azure Storage: Pokud již nemáte, můžete vytvořit [účtu služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Pozvání ke sdílení dat: Pozvánka z Microsoft Azure s předmětem s názvem "sdílené složky služby Azure Data pozvání **<yourdataprovider@domain.com>** ".

> [!IMPORTANT]
> Přijmout a přijímat Data sdílené složky Azure, musíte se nejprve zaregistrovat poskytovatele prostředků Microsoft.DataShare a vy musíte být vlastníkem, který můžete přijímat data do účtu úložiště. Postupujte podle pokynů uvedených v [Poradce při potížích s Azure Data sdílet Náhled](data-share-troubleshoot.md) zaregistrujte poskytovatele prostředků sdílet data také přidejte sami sebe jako vlastníka účtu úložiště. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Otevřít pozvánku

Zkontrolujte svoji doručenou poštu pro pozvání od vašeho zprostředkovatele. Pozvánka je z Microsoft Azure s názvem **sdílené složky služby Azure Data pozvání <yourdataprovider@domain.com>** . Poznamenejte si název sdílené složky, aby, potvrzujete svůj souhlas sdílené složce správná pokud existuje více pozvánky. 

Vyberte na **zobrazení pozvání** zobrazíte pozvání v Azure. Tím přejdete na zobrazení složky přijal.

![Pozvánky](./media/invitations.png "seznam pozvánek") 

Vyberte sdílenou složku, kterou chcete zobrazit. 

## <a name="accept-invitation"></a>Přijmout pozvánku
Ujistěte se, že všechna pole jsou kontrolovány, včetně **Terms of Use**. Pokud souhlasíte s podmínkami použití, bude nutné na zaškrtávací políčko označující, že souhlasíte s tím. 

![Podmínky použití](./media/terms-of-use.png "podmínky použití") 

V části *cílového účtu sdílení dat*, vyberte předplatné a skupinu prostředků, které budete nasazovat sdílená Data do. 

Pro **účet sdílet Data** pole, vyberte **vytvořit nový** Pokud nemáte existující účet Data sdílené složky. V opačném případě vyberte existující účet sdílet Data, která chcete přijmout sdílená data do. 

Pro *přijatých název sdílené složky* pole můžete ponechat výchozí nastavení určená Data poskytují nebo zadejte nový název pro přijaté sdílenou složku. 

![Cíl dat sdílené složky účet](./media/target-data-share.png "cílová data sdílet účet") 

Po souhlasit s podmínkami použití a zadat umístění pro svou sdílenou složku, vyberte na *přijmout a konfigurovat*. Pokud si vyberete tuhle možnost, sdílet odběr bude vytvořen a na další obrazovce vás vyzve k vyberte cílový účet úložiště pro vaše data, které se mají zkopírovat do. 

![Přijměte možnosti](./media/accept-options.png "přijměte možnosti") 

Pokud chcete přijmout pozvánku teď ale nakonfigurovat úložiště později, vyberte *přijmout a nakonfigurovat později*. Tato možnost umožňuje konfigurovat později cílový účet úložiště. A pokračujte v konfiguraci úložiště později, naleznete v tématu [konfigurace vašeho účtu úložiště](how-to-configure-mapping.md) stránky pro podrobné pokyny o tom, jak obnovit data sdílet konfigurace. 

Pokud nechcete, aby k přijetí pozvánky, vyberte *odmítnout*. 

## <a name="configure-storage"></a>Konfigurace úložiště
V části *nastavení cílové úložiště*, vyberte předplatné, skupinu prostředků a účtu úložiště, který chcete zobrazit vaše data do. 

![Cílové úložiště nastavení](./media/target-storage-settings.png "cílového úložiště") 

Pokud chcete dostávat pravidelné aktualizace dat, ujistěte se, že jste povolili nastavení snímků. Všimněte si, že se zobrazí pouze plánovaná nastavení snímku poskytovatele dat je zahrnuta ve sdílené složce data. 

![Nastavení snímku](./media/snapshot-settings.png "nastavení snímku") 

Vyberte *Uložit*. 

## <a name="trigger-a-snapshot"></a>Aktivační událost snímku

Můžete aktivovat snímků ve sdílených složkách přijatých -> karta Podrobnosti tak, že vyberete **aktivační událost snímku**. Tady můžete aktivovat úplné nebo přírůstkové snímek vaše data. Pokud je poprvé přijímání dat od poskytovatele dat, vyberte úplné kopie. 

![Aktivační událost snímku](./media/trigger-snapshot.png "snímku aktivační událost") 

Pokud je stav posledního spuštění *úspěšné*, otevřít účet úložiště, chcete-li zobrazit přijatá data. 

Chcete-li zkontrolovat, který jste použili účet úložiště, vyberte na **datových sad**. 

![Datové sady příjemce](./media/consumer-datasets.png "mapování uživatelů datové sady") 

## <a name="view-history"></a>Zobrazení historie
Chcete-li zobrazit historii vaší snímky, přejděte do složky přijal -> Historie. Tady najdete historii všech snímků, které byly generovány za posledních 60 dnů. 

## <a name="next-steps"></a>Další postup
V tomto kurzu učení jak přijmout a přijímat Data sdílené složky Azure. Další informace o konceptech Azure sdílení dat, [koncepty: Azure Data sdílet terminologie](terminology.md).