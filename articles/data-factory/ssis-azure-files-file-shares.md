---
title: Otevírání a ukládání souborů s SSIS balíčky nasazenými v Azure
description: Naučte se otevírat a ukládat soubory místně a v Azure při zvedání a přesunu balíčků SSIS, které používají místní souborové systémy, do SSIS v Azure.
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 97ed9b6f3f21b6d9e220c1f4e17d4ba6b0d8b485
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98555504"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Otevírání a ukládání souborů místně a v Azure s balíčky SSIS nasazenými v Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak otevřít a uložit soubory místně a v Azure při zvedání a přesunu balíčků SSIS, které používají místní souborové systémy, do SSIS v Azure.

## <a name="save-temporary-files"></a>Uložit dočasné soubory

Pokud potřebujete uložit a zpracovat dočasné soubory během jednoho spuštění balíčku, mohou balíčky použít aktuální pracovní adresář ( `.` ) nebo dočasnou složku ( `%TEMP%` ) Azure-SSIS Integration runtimech uzlů.

## <a name="use-on-premises-file-shares"></a>Použití místních sdílených složek

Pokud chcete i nadále používat místní **sdílené složky** při zvedání a posunování balíčků, které používají lokální souborové systémy, do SSIS v Azure, udělejte následující věci:

1. Přeneste soubory z místních souborových systémů do místních sdílených složek.

2. Připojte se k místním sdíleným složkám do služby Azure Virtual Network.

3. Připojte svůj Azure-SSIS IR ke stejné virtuální síti. Další informace najdete v tématu [připojení prostředí Azure-SSIS Integration runtime k virtuální síti](./join-azure-ssis-integration-runtime-virtual-network.md).

4. Připojte svůj Azure-SSIS IR k místním sdíleným složkám ve stejné virtuální síti nastavením přihlašovacích údajů pro přístup, které používají ověřování systému Windows. Další informace najdete v tématu [připojení k datům a sdíleným složkám pomocí ověřování systému Windows](ssis-azure-connect-with-windows-auth.md).

5. Aktualizujte cesty k místním souborům v balíčcích na cesty UNC odkazující na místní sdílené složky. Například aktualizujte `C:\abc.txt` na `\\<on-prem-server-name>\<share-name>\abc.txt` .

## <a name="use-azure-file-shares"></a>Použití sdílených složek Azure

Pokud chcete používat **soubory Azure** při zvedání a posunování balíčků, které používají místní systémy souborů, do SSIS v Azure, udělejte tyto věci:

1. Přeneste soubory z místních systémů souborů do souborů Azure. Další informace najdete v tématu [soubory Azure](https://azure.microsoft.com/services/storage/files/).

2. Připojte svůj Azure-SSIS IR k souborům Azure nastavením přihlašovacích údajů pro přístup, které používají ověřování systému Windows. Další informace najdete v tématu [připojení k datům a sdíleným složkám pomocí ověřování systému Windows](ssis-azure-connect-with-windows-auth.md).

3. Aktualizujte cesty k místním souborům v balíčcích na cesty UNC ukazující na soubory Azure. Například aktualizujte `C:\abc.txt` na `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt` .

## <a name="next-steps"></a>Další kroky

- Nasaďte balíčky. Další informace najdete v tématu [nasazení projektu SSIS do Azure pomocí SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Spusťte balíčky. Další informace najdete v tématu [spuštění balíčků SSIS v Azure pomocí SSMS](/sql/integration-services/ssis-quickstart-run-ssms).
- Naplánujte balíčky. Další informace najdete v tématu [plánování balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).