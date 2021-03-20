---
title: Zotavení po havárii pro FarmBeats
description: Tento článek popisuje, jak obnovení dat chrání před ztrátou vašich dat.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-ummehabiba
ms.openlocfilehash: 9ece624546cab1b8b6fab8c19f4401bd050f6267
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179880"
---
# <a name="disaster-recovery-for-farmbeats"></a>Zotavení po havárii pro FarmBeats

Obnovení dat chrání před ztrátou vašich dat v události, jako je sbalení oblasti Azure. V takovém případě můžete spustit převzetí služeb při selhání a obnovit data uložená ve vašem nasazení FarmBeats.

Obnovení dat není výchozí funkcí v Azure FarmBeats. Tuto funkci můžete nakonfigurovat ručně tak, že nakonfigurujete požadované prostředky Azure, které používá FarmBeats k ukládání dat do spárované oblasti Azure. K povolení obnovení použijte aktivní – pasivní přístup.

Následující části obsahují informace o tom, jak můžete v Azure FarmBeats nakonfigurovat obnovení dat:

- [Povolit redundanci dat](#enable-data-redundancy)
- [Obnovit službu z online zálohování](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Povolit redundanci dat

FarmBeats ukládá data do tří služeb Azure First Services, což jsou služby **Azure Storage**, **Cosmos DB** a **Time Series Insights**. Pomocí následujících kroků povolte redundanci dat pro tyto služby do spárované oblasti Azure:

1.  **Azure Storage** – postupujte podle těchto pokynů a povolte redundanci dat pro každý účet úložiště ve vašem nasazení FarmBeats.
2.  **Azure Cosmos DB** – podle těchto pokynů povolte redundanci dat pro Cosmos DB účet nasazení FarmBeats.
3.  **Azure Time Series Insights (TSI)** – TSI aktuálně nenabízí redundanci dat. Pokud chcete obnovit data Time Series Insights, přehrajte na svůj senzor nebo počasí a znovu nahrajte data do nasazení FarmBeats.

## <a name="restore-service-from-online-backup"></a>Obnovit službu z online zálohování

Můžete iniciovat převzetí služeb při selhání a obnovit uložená data, pro která jsou v každém z výše zmíněných úložišť dat pro nasazení FarmBeats. Po obnovení dat pro Azure Storage a Cosmos DB vytvořte v spárované oblasti Azure další nasazení FarmBeats a potom nakonfigurujte nové nasazení tak, aby používalo data z obnovených úložišť dat (tj. Azure Storage a Cosmos DB), a to pomocí následujících kroků:

1. [Konfigurace Cosmos DB](#configure-cosmos-db)
2. [Konfigurace účtu úložiště](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Konfigurace Cosmos DB

Zkopírujte přístupový klíč obnoveného Cosmos DB a aktualizujte nové Key Vault FarmBeats DataHub.


  ![Snímek obrazovky, který zvýrazní, kde získat kopii přístupového klíče](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Zkopírujte adresu URL obnoveného Cosmos DB a aktualizujte ji v nové konfiguraci App Service FarmBeats DataHub. Nyní můžete Cosmos DB účet odstranit v novém nasazení FarmBeats.

  ![Snímek obrazovky, který ukazuje, kam zkopírovat adresu URL obnoveného Cosmos DB.](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Konfigurace účtu úložiště

Zkopírujte přístupový klíč obnoveného účtu úložiště a aktualizujte ho v New FarmBeats DataHub Key Vault.

![Snímek obrazovky, který ukazuje, kam zkopírovat přístupový klíč obnoveného účtu úložiště.](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Ujistěte se, že jste v novém konfiguračním souboru FarmBeats Batch VM aktualizovali název účtu úložiště.

![Zotavení po havárii](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Podobně pokud jste povolili obnovení dat pro účet úložiště akcelerátoru, postupujte podle pokynů v kroku 2 a aktualizujte přístupový klíč a název účtu úložiště akcelerátoru v nové instanci FarmBeats.
