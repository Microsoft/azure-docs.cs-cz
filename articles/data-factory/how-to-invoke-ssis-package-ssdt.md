---
title: Spouštění balíčků SSIS z SSDT
description: Naučte se spouštět balíčky SSIS v Azure z SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 5f21623af9b89bbb020063dfb72f7b60e65a6ebe
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927708"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Spouštění balíčků SSIS v Azure z SSDT
Tento článek popisuje funkci projektů služba SSIS (SQL Server Integration Services) (SSIS) s podporou Azure na SQL Server Data Tools (SSDT), která umožňuje spouštět balíčky v Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF).  Tuto funkci můžete použít k otestování stávajících balíčků SSIS ještě před tím, než je převedete & Shift nebo migrujete do Azure, nebo pokud chcete vyvíjet nové balíčky SSIS pro spuštění v Azure.

Pomocí této funkce můžete vytvořit novou Azure-SSIS IR nebo připojit existující SSIS k projektům a pak na něm spustit balíčky.  Podporujeme spouštění balíčků pro nasazení do katalogu SSIS (SSISDB) v modelu nasazení projektu a ty, které se mají nasadit do systémů souborů/sdílených složek/souborů Azure v modelu nasazení balíčku. 

## <a name="prerequisites"></a>Předpoklady
Pokud chcete tuto funkci použít, Stáhněte si a nainstalujte nejnovější SSDT s rozšířením projektů SSIS pro Visual Studio z [tohoto místa](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) nebo jako samostatný instalační program z [tohoto místa](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Azure – povolení projektů SSIS
V SSDT můžete pomocí šablony **integračních služeb (Azure-Enabled)** vytvořit nové projekty SSIS s podporou Azure.

![Nový projekt SSIS s podporou Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Alternativně můžete Azure povolit existující projekty SSIS kliknutím pravým tlačítkem myši na uzel projektu v Průzkumník řešení panelu SSDT, aby se místní nabídka zobrazovala a pak se vybrala položka nabídky **Azure s podporou Azure** .

![Azure – povolit existující projekt SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Azure – povolení vašich stávajících projektů SSIS vyžaduje, abyste si jako verzi cílového serveru nastavili nejnovější verzi, kterou podporuje Azure-SSIS IR, která je aktuálně **SQL Server 2017**, takže pokud jste to ještě neudělali, otevře se dialogové okno s tímto nastavením.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Připojení projektů s podporou Azure k SSIS v Azure Data Factory
Připojením projektů s podporou Azure tak, aby se SSIS v ADF, můžete nahrát balíčky do souborů Azure a spustit je na Azure-SSIS IR.  Provedete to následujícím postupem:

1. Kliknutím pravým tlačítkem myši na uzel projekt nebo **propojený uzel prostředky Azure** na panelu Průzkumník řešení SSDT otevřete nabídku a vyberte možnost **připojit k SSIS v nabídce Azure Data Factory** položky nabídky, aby se v **Průvodci připojením ADF spustil SSIS**.

   ![Připojení k SSIS v ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Na **úvodní stránce SSIS na ADF** se podívejte na Úvod a pokračujte kliknutím na tlačítko **Další** .

   ![SSIS v úvodu ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Na stránce **Vyberte SSIS IR v ADF** vyberte existující ADF a Azure-SSIS IR, jestli chcete spustit balíčky, nebo vytvořte nové, pokud žádné nemáte.
   - Pokud chcete vybrat existující Azure-SSIS IR, vyberte nejdřív příslušné předplatné Azure a ADF.
   - Pokud vyberete svůj existující ADF, který nemá žádné Azure-SSIS IR, klikněte na tlačítko **vytvořit SSIS IR** a vytvořte nový na portálu ADF nebo v aplikaci.
   - Pokud vyberete stávající předplatné Azure, které nemá žádný ADF, klikněte na tlačítko **vytvořit SSIS IR** a spusťte **průvodce vytvořením Integration runtime**, kde můžete zadat umístění a předponu, abychom mohli automaticky vytvořit novou skupinu prostředků Azure, Data Factory a SSIS IR vaším jménem, a to s názvem v následujícím vzoru: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Výběr SSIS IR v ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Na stránce **vybrat Azure Storage** vyberte svůj existující účet Azure Storage, abyste do souborů Azure nahráli balíčky, nebo vytvořte novou, pokud žádné nemáte.
   - Pokud chcete vybrat svůj existující účet Azure Storage, vyberte nejdřív příslušné předplatné Azure.
   - Pokud zaškrtnete stejné předplatné Azure jako Azure-SSIS IR, která nemá žádný účet Azure Storage, klikněte na tlačítko **vytvořit Azure Storage** pro nás pro automatické vytvoření nového nového uživatele ve stejném umístění, ve kterém se používá, a na stejné místo jako vaše Azure-SSIS IR s názvem kombinováním předpony svého Azure-SSIS IRho názvu a jeho data vytvoření.
   - Pokud vyberete jiné předplatné Azure, které nemá žádný účet Azure Storage, klikněte na tlačítko **vytvořit Azure Storage** a vytvořte nový na Azure Portal.
   
   ![Výběr služby Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Kliknutím na tlačítko **připojit** dokončete připojení.  Vybraný Azure-SSIS IR a Azure Storage účet zobrazíme v uzlu **propojených prostředků Azure** na panelu Průzkumník řešení na panelu SSDT.  Aktualizujeme taky stav vašeho Azure-SSIS IR, ale můžete ho spravovat tak, že pravým tlačítkem myši kliknete na jeho uzel a pak vyberete položku nabídky **Start\Stop\Manage** , která vás přesměruje na portál a aplikaci ADF.

## <a name="execute-ssis-packages-in-azure"></a>Spouštění balíčků SSIS v Azure
### <a name="starting-package-executions"></a>Spouštění spouštění balíčků
Po připojení projektů k SSIS v ADF můžete spouštět balíčky na Azure-SSIS IR.  Spuštění balíčku můžete spustit dvěma způsoby:
-  Kliknutím na tlačítko **Start** v panelu nástrojů SSDT vyřaďte nabídku a vyberte položku nabídky **Spustit v Azure** . 

   ![Provést v Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Kliknutím pravým tlačítkem na uzel balíčku v Průzkumník řešení panelu SSDT se otevře nabídka a v položce nabídky Azure vyberte možnost **spustit balíček** .

   ![Spustit balíček v Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Spouštění balíčků v Azure vyžaduje, abyste měli spuštěný Azure-SSIS IR, takže pokud je Azure-SSIS IR zastavený, zobrazí se dialogové okno, aby se spouštělo.  S výjimkou času vlastní instalace by tento proces měl být dokončen během 5 minut, ale může trvat přibližně 20-30 minut, než se Azure-SSIS IR připojuje k virtuální síti.  Po spuštění balíčků v Azure můžete zastavit Azure-SSIS IR a spravovat tak jejich spuštěné náklady tak, že pravým tlačítkem myši kliknete na svůj uzel v Průzkumník řešením panelu SSDT a pak vyberete položku nabídky **Start\Stop\Manage** , která vás přesměruje na portál ADF nebo na aplikaci.

### <a name="checking-package-execution-logs"></a>Kontrola protokolů spuštění balíčku
Při spuštění balíčku naformátujeme a zobrazíme protokol v okně průběhu SSDT.  V případě dlouhotrvajícího balíčku budeme průběžně aktualizovat protokol o minuty.  Spuštění balíčku můžete zastavit kliknutím na tlačítko **zastavit** na panelu nástrojů SSDT, který ho okamžitě zruší.  Nezpracované data protokolu můžete také dočasně najít v cestě UNC (Universal Naming Convention): `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`, ale po jednom dni ji vyčistíme.

### <a name="switching-package-protection-level"></a>Přepínání úrovně ochrany balíčků
Spouštění balíčků SSIS v Azure nepodporuje úrovně ochrany **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** .  V důsledku toho budou v případě, že jsou balíčky nakonfigurovány s těmito balíčky, dočasně je přepnete do **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**v uvedeném pořadí s náhodně generovanými hesly, když nahrajeme vaše balíčky do služby soubory Azure ke spuštění na vašich Azure-SSIS IR.

> [!NOTE]
> Pokud vaše balíčky obsahují úlohy vykonání balíčku, které odkazují na jiné balíčky nakonfigurované s úrovní ochrany **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** , musíte tyto ostatní balíčky před spuštěním balíčků ručně znovu nakonfigurovat tak, aby používaly **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**.

Pokud jsou vaše balíčky už nakonfigurované s úrovní ochrany **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** , budeme je uchovávat beze změny, ale při nahrávání vašich balíčků do souborů Azure budou pořád používat náhodně vytvořená hesla, která se budou spouštět na vašich Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Použití konfiguračního souboru balíčku
Pokud v modelu nasazení balíčku použijete konfigurační soubory balíčku pro změnu hodnot proměnných v době běhu, budeme tyto soubory automaticky nahrát do souborů Azure, aby je bylo možné vykonání na vašem Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Pomocí úlohy spustit balíček
Pokud vaše balíčky obsahují úlohy vykonání balíčku, které odkazují na jiné balíčky uložené v místních systémech souborů, musíte provést následující další nastavení:

1. Nahrajte ostatní balíčky do souborů Azure pod stejný účet Azure Storage připojený k vašim projektům a získejte novou cestu UNC, třeba `\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Nahraďte cestu k souborům pro tyto ostatní balíčky ve Správci připojení k souboru pro provádění úloh balíčku s novou cestou UNC.
   - Pokud Váš počítač se systémem SSDT nemá přístup k nové cestě UNC, můžete změnit cestu k souboru na panelu Vlastnosti v souboru Správce připojení.
   - Alternativně můžete použít proměnnou pro cestu k souboru pro přiřazení správné hodnoty v době běhu.

Pokud vaše balíčky obsahují úlohy vykonání balíčku, které odkazují na jiné balíčky ve stejném projektu, není nutné žádné další nastavení.

## <a name="next-steps"></a>Další kroky
Až budete s používáním balíčků v Azure spokojeni z SSDT, můžete je nasadit a spustit jako aktivity balíčku SSIS v kanálech ADF. Další informace najdete v tématu [spouštění balíčků SSIS jako spouštění aktivit balíčku SSIS v kanálech ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
