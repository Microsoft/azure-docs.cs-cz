---
title: Zotavení po havárii pro FarmBeats
description: Tento článek popisuje, jak obnovení dat chrání před ztrátou dat.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: fbda28ce588aad5f6bc0d89de60069c4220fa523
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266172"
---
# <a name="disaster-recovery-for-farmbeats"></a>Zotavení po havárii pro FarmBeats

Obnovení dat chrání před ztrátou dat v události, jako je kolaps oblasti Azure. V takovém případě můžete spustit převzetí služeb při selhání a obnovit data uložená v nasazení FarmBeats.

Obnova dat není výchozí funkce v Azure FarmBeats. Tuto funkci můžete nakonfigurovat ručně konfigurací požadovaných prostředků Azure, které farmbeaty používají k ukládání dat v oblasti spárované azure. Použití aktivní – pasivní přístup k povolení obnovení.

Následující části obsahují informace o tom, jak můžete nakonfigurovat obnovu dat v Azure FarmBeats:

- [Povolit redundanci dat](#enable-data-redundancy)
- [Obnovení služby ze zálohy online](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Povolit redundanci dat

FarmBeats ukládá data ve třech službách Azure první strany, což jsou **Azure storage**, **Cosmos DB** a Time Series **Insights**. Pomocí následujících kroků povolte redundanci dat pro tyto služby do spárované oblasti Azure:

1.  **Azure Storage** – postupujte podle těchto pokynů a povolte redundanci dat pro každý účet úložiště v nasazení FarmBbeats.
2.  **Azure Cosmos DB** – postupujte podle těchto pokynů a povolte redundanci dat pro účet Cosmos DB, které nasazuje vaše FarmBeats.
3.  **Azure Time Series Insights (TSI)** – TSI momentálně nenabízí redundanci dat. Chcete-li obnovit data Time Series Insights, přejděte na svého partnera pro senzory a počasí a znovu je převeďte do nasazení FarmBeats.

## <a name="restore-service-from-online-backup"></a>Obnovení služby ze zálohy online

Můžete zahájit převzetí služeb při selhání a obnovit uložená data, pro která, každý z výše uvedených datových úložišť pro nasazení FarmBeats. Po obnovení dat pro úložiště Azure a Cosmos DB vytvořte další nasazení FarmBeats ve spárované oblasti Azure a pak nakonfigurujte nové nasazení tak, aby používalo data z obnovených úložišť dat (tj. Azure Storage a Cosmos DB) pomocí následujících kroků:

1. [Konfigurace Cosmos DB](#configure-cosmos-db)
2. [Konfigurace účtu úložiště](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Konfigurace Cosmos DB

Zkopírujte přístupový klíč obnovené databáze Cosmos db a aktualizujte nový trezor klíčů FarmBeats Datahub.


  ![Zotavení po havárii](./media/disaster-recovery-for-farmbeats/keyvault-secrets.png)

> [!NOTE]
> Zkopírujte adresu URL obnovené databáze Cosmos a aktualizujte ji v nové konfiguraci služby FarmBeats Datahub App Service. Nyní můžete odstranit účet Cosmos DB v novém nasazení FarmBeats.

  ![Zotavení po havárii](./media/disaster-recovery-for-farmbeats/northeu-ehub-api-configuration.png)

### <a name="configure-storage-account"></a>Konfigurace účtu úložiště

Zkopírujte přístupový klíč obnoveného účtu úložiště a aktualizujte jej v novém trezoru klíčů FarmBeats Datahub Key Vault.

![Zotavení po havárii](./media/disaster-recovery-for-farmbeats/keyvault-7udqm-secrets.png)

>[!NOTE]
> Ujistěte se, že chcete aktualizovat název účtu úložiště v novém konfiguračním souboru virtuálního virtuálního zařízení FarmBeats Batch.

![Zotavení po havárii](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Podobně pokud jste povolili obnovu dat pro váš účet úložiště akcelerátoru, postupujte podle kroku 2 aktualizovat klíč a název účtu úložiště akcelerátoru v nové instanci FarmBeats.
