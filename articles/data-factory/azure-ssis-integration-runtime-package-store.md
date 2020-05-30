---
title: Správa balíčků pomocí Azure-SSIS Integration Runtimeho úložiště balíčků
description: Naučte se spravovat balíčky pomocí Azure-SSIS Integration Runtimeového úložiště balíčků.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84199053"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Správa balíčků pomocí Azure-SSIS Integration Runtimeho úložiště balíčků

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pokud chcete napředt & přesun místních úloh služba SSIS (SQL Server Integration Services) (SSIS) do cloudu, můžete v Azure Data Factory (ADF) zřídit Azure-SSIS Integration Runtime (IR). Další informace najdete v tématu [zřízení Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure). Azure-SSIS IR podporuje:

- Spouštění balíčků nasazených do katalogu SSIS (SSISDB) Azure SQL Database hostovaných serverem/spravovanou instancí (model nasazení projektu)
- Spouštění balíčků nasazených do systému souborů, souborů Azure nebo databáze služby SQL Server Database (MSDB) hostované službou Azure SQL Managed instance (model nasazení balíčku)

Když použijete model nasazení balíčku, můžete si vybrat, jestli chcete Azure-SSIS IR zřídit s obchody s balíčky, které poskytují vrstvu správy balíčků, a to nad systémem souborů/soubory Azure/MSDB hostované službou Azure SQL Managed instance. Azure-SSIS IR úložiště balíčků umožňuje importovat/exportovat, odstraňovat a spouštět balíčky a monitorovat nebo zastavovat balíčky přes SQL Server Management Studio (SSMS) podobně jako [starší verze úložiště balíčků SSIS](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Připojení k Azure-SSIS IR

Po zřízení Azure-SSIS IR se k němu můžete připojit a procházet jeho úložiště balíčků na SSMS.

![Připojení k Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

V **Průzkumník objektům** okně SSMS vyberte v rozevírací nabídce **připojit** možnost **Azure-SSIS Integration runtime** . Potom se přihlaste k Azure a vyberte příslušné předplatné, ADF a Azure-SSIS IR, ke kterým jsou připojené vaše úložiště balíčků. Vaše Azure-SSIS IR se zobrazí se **spuštěnými balíčky** a uzly **uložených balíčků** pod. Rozbalte uzel **uložené balíčky** , abyste viděli úložiště balíčků pod. Rozbalením úložišť balíčků Zobrazte složky a balíčky pod. Může se zobrazit výzva k zadání přihlašovacích údajů pro úložiště balíčků, pokud se k nim SSMS nepřipojí automaticky. Pokud například rozbalíte úložiště balíčků v databázi MSDB, může být nejprve požádáno o připojení ke spravované instanci Azure SQL.

![Připojit ke spravované instanci Azure SQL](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Správa složek a balíčků

Když procházíte Azure-SSIS IR na SSMS, můžete kliknout pravým tlačítkem na libovolné úložiště balíčků/složky/balíčky a automaticky se otevře nabídka a vybrat **Nová složka**, **importovat balíček**, **Exportovat balíček**, **Odstranit**nebo **aktualizovat**.

   ![Správa složek a balíčků](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Výběrem **nové složky** vytvořte novou složku pro importované balíčky.

   *  Vyberte **importovat balíček** pro import balíčků ze **systému souborů**, **SQL Server** (msdb) nebo z staršího **úložiště balíčků SSIS** do úložiště balíčků.

      ![Importovat balíček](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      V závislosti na **umístění balíčku** , ze kterého se má importovat, vyberte příslušný **Server** / **typ ověřování**serveru, zadejte přihlašovací údaje v případě potřeby, vyberte **cestu k balíčku**a zadejte nový **název balíčku**. Při importu balíčků nejde změnit jejich úroveň ochrany. Pokud ho chcete změnit, použijte nástroje pro SQL Server Data Tools (SSDT) nebo `dtutil` Nástroj příkazového řádku.

   *  Vyberte **Exportovat balíček** pro export balíčků z úložiště balíčků do **systému souborů**, **SQL Server** (msdb) nebo starší verze **úložiště balíčků SSIS**.

      ![Exportovat balíček](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      V závislosti na **umístění balíčku** , do kterého se má exportovat, vyberte **Server**příslušný / **typ ověřování**serveru, v případě potřeby zadejte přihlašovací údaje a vyberte **cestu k balíčku**. Při exportování balíčků, pokud jsou zašifrované, zadejte hesla, která chcete nejdřív dešifrovat, a pak můžete změnit jejich úroveň ochrany, například Neukládat citlivá data nebo šifrovat všechna/citlivá data pomocí klíče/hesla uživatele.

   *  Vyberte **Odstranit** , pokud chcete z úložiště balíčků odstranit existující složky nebo balíčky.

   *  Výběrem **aktualizovat** zobrazíte nově přidané složky/balíčky v úložišti balíčků.

## <a name="execute-packages"></a>Spustit balíčky

Když procházíte Azure-SSIS IR na SSMS, můžete kliknout pravým tlačítkem na libovolné uložené balíčky a automaticky se otevře nabídka a vybrat **spustit balíček**.  Tím se otevře dialogové okno **Spustit nástroj balíčku** , kde můžete nakonfigurovat spouštění balíčků na Azure-SSIS IR jako spustit aktivity balíčku SSIS v kanálech ADF.

![Spustit stránky nástroje balíčku 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Spustit stránky nástroje balíčku 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

Stránka **Obecné**, **Konfigurace**, **Možnosti spuštění**a **protokolování** v dialogovém okně **Spustit nástroj balíčku** se shoduje s kartou **Nastavení** aktivity spustit balíček SSIS, kde můžete zadat šifrovací heslo pro váš balíček, získat přístup k informacím o konfiguračním souboru balíčku, přihlašovací údaje pro spuštění balíčku nebo vlastnosti a získat přístup k informacím pro složku protokolu.  Stránka **nastavit hodnoty** v dialogovém okně **Spustit nástroj balíčku** odpovídá kartě **přepsání vlastností** aktivity spustit balíček SSIS, kde můžete zadat existující vlastnosti balíčku, které chcete přepsat. Další informace najdete v tématu [spouštění balíčků SSIS jako aktivity balíčku SSIS v kanálech ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Když v dialogovém okně **Spustit nástroj balíčku** vyberete tlačítko **Spustit** , nový kanál ADF s aktivitou spustit SSIS se automaticky vygeneruje a aktivuje. Pokud už existuje kanál ADF se stejným nastavením spuštění balíčku, spustí se znovu a nový kanál se nevygeneruje. Aktivita kanálu ADF a spustit balíček SSIS budou pojmenovány `Pipeline_SSMS_YourPackageName_HashString` a v `Activity_SSMS_YourPackageName` uvedeném pořadí.

![Tlačítko spustit nástroj pro balíček](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Aktivita provádění balíčku SSIS](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Monitorování a zastavení spouštění balíčků

Když procházíte Azure-SSIS IR na SSMS, můžete rozbalit uzel **běžící balíčky** a zobrazit tak aktuálně spuštěné balíčky pod.  Kliknutím pravým tlačítkem myši na libovolnou z nich můžete zobrazit nabídku a vybrat **zastavit** nebo **aktualizovat**.

   ![Monitorování a zastavení spouštění balíčků](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Výběrem možnosti **zastavit** zrušíte aktuálně běžící kanál ADF, který spouští balíček jako aktivitu spustit SSIS balíček.

   *  Výběrem **aktualizovat** zobrazíte nově běžící balíčky z úložišť balíčků.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Monitorování Azure-SSIS IR a úpravy úložišť balíčků

Při procházení Azure-SSIS IR na SSMS můžete kliknout pravým tlačítkem myši na tuto nabídku a vybrat **Přejít na Azure Data Factory Portal** nebo **aktualizovat**.

   ![Přejít na portál ADF](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Výběrem možnosti **Přejít na Azure Data Factory Portal** otevřete stránku **Integration runtime** centra monitorování ADF, kde můžete monitorovat Azure-SSIS IR. Na dlaždici **úložiště balíčků** můžete zobrazit počet úložišť balíčků, které jsou připojené k vašemu Azure-SSIS IR.  Výběrem tohoto čísla se zobrazí okno, kde můžete upravit propojené služby ADF, které ukládají informace o přístupu k úložištím balíčků.

      ![Upravit úložiště balíčků](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Výběrem **aktualizovat** zobrazíte nově přidané složky/balíčky v úložištích balíčků a spustíte balíčky z úložišť balíčků.

## <a name="next-steps"></a>Další kroky

Automaticky generované kanály ADF můžete znovu spustit pomocí aktivit balíčku Execute SSIS nebo vytvářet nové v portálu ADF. Další informace najdete v tématu [spouštění balíčků SSIS jako aktivity balíčku SSIS v kanálech ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).