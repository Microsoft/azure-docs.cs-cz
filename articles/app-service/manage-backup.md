---
title: Zálohování aplikace – Azure App Service
description: Zjistěte, jak vytvářet zálohy aplikací ve službě Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6a43edc6e8bf0afe28f27f4be53315c5e807dc8d
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338576"
---
# <a name="back-up-your-app-in-azure"></a>Zálohování aplikace v Azure
Funkce zálohování a obnovení v [služby Azure App Service](overview.md) umožňuje snadno vytvářet zálohy aplikací ručně, nebo podle plánu. Aplikaci můžete obnovit na snímek v předchozím stavu tak přepsání existující aplikace nebo při obnovování do jiné aplikace. 

Informace o obnovení aplikace ze zálohy najdete v tématu [obnovení aplikace v Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Co se zálohuje
App Service můžete zálohovat následující informace do účtu služby Azure storage a kontejner, který jste nakonfigurovali vaše aplikace bude moct používat. 

* Konfigurace aplikace
* Obsah souboru
* Databáze připojené k vaší aplikace

Podporují se následující řešení databáze s funkcí zálohování: 
   - [SQL Database](https://azure.microsoft.com/services/sql-database/)
   - [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
   - [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
   - [MySQL v aplikaci](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Každá záloha je úplnou kopií offline aplikace, ne přírůstkové aktualizace.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Požadavky a omezení
* Funkce zálohování a obnovení vyžaduje plán služby App Service v **standardní** vrstvy nebo **Premium** vrstvy. Další informace o škálování plánu služby App Service na vyšší úroveň můžete použít, najdete v části [vertikální navýšení kapacity aplikace v Azure](web-sites-scale.md).  
  **Premium** úroveň umožňuje větší počet denních zpět ups než **standardní** vrstvy.
* Potřebujete účet úložiště Azure a kontejnerů ve stejném předplatném jako aplikace, které chcete zálohovat. Další informace o účtech Azure storage najdete v tématu [přehled účtu Azure storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview).
* Zálohování může být až 10 GB aplikaci a databázi obsahu. Pokud velikost zálohování překračuje tento limit, dojde k chybě.
* Zálohy protokolu SSL povoleno databáze Azure pro MySQL se nepodporuje. Pokud zálohování se nenakonfiguruje, zobrazí se Nezdařená zálohování.
* Zálohy protokolu SSL povoleno – Azure Database for postgresql – není podporované. Pokud zálohování se nenakonfiguruje, zobrazí se Nezdařená zálohování.
* Databáze MySQL v aplikaci se automaticky zálohují bez jakékoli konfigurace. Pokud provedete ruční nastavení pro databáze MySQL v aplikaci, jako je například přidávání připojovací řetězce, bude zálohování nemusí fungovat správně.
* Účet úložiště pomocí brány firewall povolit, protože cílové umístění zálohy, které se nepodporuje. Pokud zálohování se nenakonfiguruje, zobrazí se Nezdařená zálohování.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Vytvoření ruční zálohy
1. V [webu Azure portal](https://portal.azure.com), přejděte na stránku vaší aplikace, vyberte **zálohy**. **Zálohy** zobrazí se stránka.
   
    ![Stránka zálohy][ChooseBackupsPage]
   
   > [!NOTE]
   > Pokud se zobrazí následující zpráva, klikněte na něj chcete upgradovat plán služby App Service, abyste mohli pokračovat v zálohování.
   > Další informace najdete v tématu [vertikální navýšení kapacity aplikace v Azure](web-sites-scale.md).  
   > ![Zvolte účet úložiště](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. V **zálohování** stránce klikněte na **konfigurovat**
![klikněte na tlačítko Konfigurovat.](./media/web-sites-backup/ClickConfigure1.png)
3. V **konfigurace zálohování** klikněte na **úložiště: Není nakonfigurováno** a nakonfigurujte účet úložiště.
   
    ![Výběr účtu úložiště][ChooseStorageAccount]
4. Vyberte cílovou složku zálohy tak, že vyberete **účtu úložiště** a **kontejneru**. Účet úložiště musí patřit do stejného předplatného jako aplikace, které chcete zálohovat. Pokud chcete, můžete vytvořit nový účet úložiště nebo nový kontejner na příslušných stránkách. Jakmile budete hotovi, klikněte na tlačítko **vyberte**.
   
    ![Výběr účtu úložiště](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. V **konfigurace zálohování** stránce, která je pořád otevřená, můžete nakonfigurovat **příkaz Backup Database**, vyberte databáze, které chcete zahrnout do zálohy (SQL database nebo MySQL) a pak klikněte na tlačítko **OK**.  
   
    ![Výběr účtu úložiště](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Pro databáze se zobrazí v tomto seznamu, musí existovat svůj připojovací řetězec **připojovací řetězce** část **nastavení aplikace** stránky pro vaši aplikaci. 
   >
   > Databáze MySQL v aplikaci se automaticky zálohují bez jakékoli konfigurace. Pokud provedete ruční nastavení pro databáze MySQL v aplikaci, jako je například přidávání připojovací řetězce, bude zálohování nemusí fungovat správně.
   > 
   > 
6. V **konfigurace zálohování** klikněte na **Uložit**.    
7. V **zálohy** klikněte na **zálohování**.
   
    ![Tlačítko BackUpNow][BackUpNow]
   
    Zobrazí zprávu o průběhu během procesu zálohování.

Jakmile je nakonfigurovaný účet úložiště a kontejner, můžete zahájit ruční zálohy v každém okamžiku.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurace automatizovaného zálohování
1. V **konfiguraci zálohování** nastavte **naplánované zálohování** k **na**. 
   
    ![Výběr účtu úložiště](./media/web-sites-backup/05ScheduleBackup1.png)
2. Nastavení plánu zálohování, které se zobrazí možnosti **naplánované zálohování** k **na**, podle potřeby nakonfigurujte plán zálohování a klikněte na tlačítko **OK**.
   
    ![Povolit automatické zálohování][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Nakonfigurujte částečné zálohy
Někdy nechcete zálohovat vše, co ve vaší aplikaci. Tady je pár příkladů:

* Můžete [nastavení týdenní zálohy](#configure-automated-backups) vaší aplikace, který obsahuje statický obsah, který se nikdy nemění, třeba staré blogové příspěvky nebo Image.
* Vaše aplikace má více než 10 GB obsahu (tj. maximální hodnota, kterou můžete zálohovat v čase).
* Nechcete k zálohování souborů protokolu.

Částečné obnovy umožní že zvolit přesně soubory, které chcete zálohovat.

### <a name="exclude-files-from-your-backup"></a>Vyloučit soubory ze zálohy
Předpokládejme, že máte aplikaci, která obsahuje soubory protokolů a statické obrázky, které byly záložní jednou a nebudou měnit. V takových případech můžete vyloučit tyto složky a soubory ukládat v budoucích zálohování. Vyloučit soubory a složky ze záloh, vytváření `_backup.filter` soubor `D:\home\site\wwwroot` složky vaší aplikace. Zadejte seznam souborů a složek, které chcete vyloučit z tohoto souboru. 

Snadný způsob, jak přístup k souborům je použití Kudu. Klikněte na tlačítko **Rozšířené nástroje -> Přejít** nastavení pro vaši webovou aplikaci pro přístup k Kudu.

![Kudu pomocí portálu][kudu-portal]

Identifikujte složky, které chcete vyloučit ze zálohy.  Například chcete vyfiltrovat zvýrazněné složky a soubory.

![Složka Obrázky][ImagesFolder]

Vytvořte soubor s názvem `_backup.filter` a vložit souboru v předchozím seznamu, ale odebrat `D:\home`. Seznam, jeden adresář nebo soubor na každém řádku. Obsah souboru by tak měly být:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Nahrát `_backup.filter` do souboru `D:\home\site\wwwroot\` adresáři vašeho webu pomocí [ftp](deploy-ftp.md) nebo jakékoliv jiné metody. Pokud chcete, můžete vytvořit soubor přímo pomocí Kudu `DebugConsole` a vložení obsahu existuje.

Spuštění zálohování stejně jako byste to obvykle udělali, [ručně](#create-a-manual-backup) nebo [automaticky](#configure-automated-backups). Nyní, všechny soubory a složky, které jsou určené v `_backup.filter` je vyloučen z budoucí zálohy naplánované, nebo ručně zahájena. 

> [!NOTE]
> Obnovit částečné zálohy vašeho webu stejným způsobem, jakým byste [obnovení pravidelného zálohování](web-sites-restore.md). Proces obnovení dělá správné věci.
> 
> Po obnovení úplné zálohy, veškerý obsah na webu se nahradí cokoli, co je v záloze. Pokud je soubor v lokalitě, ale ne v zálohování se odstraní. Ale když se obnoví částečné zálohy veškerý obsah, který se nachází v jednom z adresářů jako zakázaný nebo jakýkoli soubor jako zakázaný, je ponechán beze změny.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Jak jsou zálohy uložené.
Po provedení jedné nebo více zálohování pro vaši aplikaci, jsou viditelné na zálohy **kontejnery** stránku vašeho účtu úložiště a vaše aplikace. V účtu úložiště, každá záloha se skládá z`.zip` soubor, který obsahuje data záloh a `.xml` soubor, který obsahuje manifest aplikace `.zip` obsah souboru. Můžete rozbalit a procházet tyto soubory, pokud chcete získat přístup k zálohování bez ve skutečnosti obnovení aplikace.

Zálohování databáze aplikace je uložená v kořenovém adresáři souboru ZIP. U databáze SQL je soubor BACPAC (bez přípony souboru) a mohou být naimportovány. Vytvoření SQL database založena na export souboru BACPAC najdete v tématu [Import souboru BACPAC k vytvoření nového uživatele databáze](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Změna některý ze souborů ve vaší **websitebackups** kontejner může způsobit zálohování stane neplatnou a proto není – obnovitelné.
> 
> 

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Můžete automatizovat správu záloh pomocí skriptů s využitím [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nebo [prostředí Azure PowerShell](/powershell/azure/overview).

Ukázky najdete v tématu:

- [Ukázky Azure CLI](samples-cli.md)
- [Ukázky Azure PowerShellu](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Další kroky
Informace o obnovení aplikace ze zálohy najdete v tématu [obnovení aplikace v Azure](web-sites-restore.md). 


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG

