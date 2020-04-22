---
title: Otevírání a ukládání souborů pomocí balíčků SSIS nasazených v Azure
description: Zjistěte, jak otevírat a ukládat soubory místně a v Azure, když zvednete a přesunete balíčky SSIS, které používají místní souborové systémy do SSIS v Azure
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 630a8ead409ad60b9146838f062210a3301b43b5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760196"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Otevírání a ukládání souborů v místním prostředí a v Azure pomocí balíčků SSIS nasazených v Azure

Tento článek popisuje, jak otevřít a uložit soubory v místním prostředí a v Azure, když zvednete a přesunete balíčky SSIS, které používají místní souborové systémy do SSIS v Azure.

## <a name="save-temporary-files"></a>Uložení dočasných souborů

Pokud potřebujete ukládat a zpracovávat dočasné soubory během jednoho spuštění balíčku, balíčky můžete použít aktuální pracovní adresář (`.`) nebo dočasné složky (`%TEMP%`) z uzly Azure-SSIS Integrace Runtime.

## <a name="use-on-premises-file-shares"></a>Použití místních sdílených složek

Pokud chcete i nadále používat **místní sdílené složky** při zvedání a přesouvání balíčků, které používají místní systémy souborů, do SSIS v Azure, proveďte následující akce:

1. Přeneste soubory z místních systémů souborů do místních sdílených složek.

2. Připojte místní sdílené složky do virtuální sítě Azure.

3. Připojte infračervený přenos Azure-SSIS ke stejné virtuální síti. Další informace najdete [v tématu Připojení za běhu integrace Azure-SSIS do virtuální sítě](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

4. Připojte azure-SSIS IR k místní sdílené složky uvnitř stejné virtuální sítě nastavením přístupových přihlašovacích údajů, které používají ověřování systému Windows. Další informace najdete v tématu [Připojení k datům a sdíleným položkám pomocí ověřování systému Windows](ssis-azure-connect-with-windows-auth.md).

5. Aktualizujte místní cesty k souborům v balíčcích na cesty UNC směřující na místní sdílené složky. Aktualizace například `C:\abc.txt` `\\<on-prem-server-name>\<share-name>\abc.txt`na .

## <a name="use-azure-file-shares"></a>Použití sdílených složek Azure

Pokud chcete používat **soubory Azure** při zvedání a přesouvání balíčků, které používají místní systémy souborů, do SSIS v Azure, proveďte následující akce:

1. Přenos souborů z místních systémů souborů do souborů Azure. Další informace najdete v tématu [Soubory Azure](https://azure.microsoft.com/services/storage/files/).

2. Připojte infračervený přenos Azure-SSIS k souborům Azure nastavením přístupových přihlašovacích údajů, které používají ověřování systému Windows. Další informace najdete v tématu [Připojení k datům a sdíleným položkám pomocí ověřování systému Windows](ssis-azure-connect-with-windows-auth.md).

3. Aktualizujte místní cesty k souborům ve vašich balíčcích na cesty UNC směřující na soubory Azure. Aktualizace například `C:\abc.txt` `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`na .

## <a name="next-steps"></a>Další kroky

- Nasaďte své balíčky. Další informace najdete [v tématu Nasazení projektu SSIS do Azure pomocí SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Spusťte své balíčky. Další informace najdete v [tématu Spuštění balíčků SSIS v Azure s SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Naplánujte si balíčky. Další informace najdete v [tématu Plánování balíčků SSIS v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).
