---
title: Zálohování aplikace – Azure App Service
description: Naučte se vytvářet zálohy vašich aplikací v Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8784a06306f59015b95293d90ff5509dcfcae045
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057939"
---
# <a name="back-up-your-app-in-azure"></a>Zálohování aplikace v Azure
Funkce zálohování a obnovení v [Azure App Service](overview.md) umožňuje snadno vytvářet zálohy aplikací ručně nebo podle plánu.  Zálohy je možné nakonfigurovat tak, aby se zachovaly až do neurčitého množství času. Aplikaci můžete obnovit do snímku předchozího stavu přepsáním existující aplikace nebo obnovením do jiné aplikace.

Informace o obnovení aplikace ze zálohy najdete v tématu [obnovení aplikace v Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Co se zálohuje
App Service můžou zálohovat následující informace do účtu služby Azure Storage a kontejneru, který jste nakonfigurovali pro použití. 

* Konfigurace aplikace
* Obsah souboru
* Databáze připojená k vaší aplikaci

Funkce zálohování podporuje následující databázová řešení: 
   - [SQL Database](https://azure.microsoft.com/services/sql-database/)
   - [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
   - [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
   - [MySQL v aplikaci](https://azure.microsoft.com/en-us/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
>  Každá záloha je kompletní offline kopie vaší aplikace, nikoli přírůstková aktualizace.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Požadavky a omezení
* Funkce zálohování a obnovení vyžaduje, aby byl plán App Service v úrovni **Standard** nebo **Premium** . Další informace o škálování plánu App Service pro použití vyšší úrovně najdete v tématu [horizontální navýšení kapacity aplikace v Azure](manage-scale-up.md).  
  Úroveň **Premium** umožňuje větší počet denních zdrojů pro zálohování než úroveň **Standard** .
* Potřebujete účet úložiště Azure a kontejner ve stejném předplatném jako aplikace, kterou chcete zálohovat. Další informace o účtech Azure Storage najdete v tématu [Přehled účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).
* Zálohy můžou mít až 10 GB obsahu aplikace a databáze. Pokud velikost zálohy překročí tento limit, zobrazí se chyba.
* Zálohy Azure Database for MySQL s povoleným protokolem SSL se nepodporují. Pokud je nakonfigurované zálohování, obdržíte neúspěšné zálohy.
* Zálohy Azure Database for PostgreSQL s povoleným protokolem SSL se nepodporují. Pokud je nakonfigurované zálohování, obdržíte neúspěšné zálohy.
* Databáze MySQL v aplikaci se automaticky zálohují bez jakýchkoli konfigurací. Pokud ručně nastavíte nastavení pro databáze MySQL v aplikaci, například přidávání připojovacích řetězců, zálohování nemusí správně fungovat.
* Použití účtu úložiště s povoleným bránou firewall, protože cíl pro zálohování není podporovaný. Pokud je nakonfigurované zálohování, obdržíte neúspěšné zálohy.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Vytvoření ruční zálohy
1. V [Azure Portal](https://portal.azure.com)přejděte na stránku vaší aplikace, vyberte **zálohování**. Zobrazí se stránka **zálohy** .
   
    ![Stránka zálohy][ChooseBackupsPage]
   
   > [!NOTE]
   > Pokud se zobrazí následující zpráva, kliknutím na ni upgradujte plán App Service, abyste mohli pokračovat v zálohování.
   > Další informace najdete v tématu [horizontální navýšení kapacity aplikace v Azure](manage-scale-up.md).  
   > ![Zvolit účet úložiště](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. Na stránce **zálohování** klikněte na **Konfigurovat**
![klikněte na konfigurovat.](./media/web-sites-backup/ClickConfigure1.png)
3. Na stránce **Konfigurace zálohování** klikněte na **úložiště: Není nakonfigurováno** pro konfiguraci účtu úložiště.
   
    ![Výběr účtu úložiště][ChooseStorageAccount]
4. Zvolte cíl zálohování a vyberte **účet úložiště** a **kontejner**. Účet úložiště musí patřit do stejného předplatného jako aplikace, kterou chcete zálohovat. Pokud chcete, můžete na příslušných stránkách vytvořit nový účet úložiště nebo nový kontejner. Až skončíte, klikněte na **Vybrat**.
   
    ![Výběr účtu úložiště](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. Na stránce **Konfigurace zálohování** , která je pořád otevřená, můžete nakonfigurovat **záložní databázi**a pak vybrat databáze, které chcete zahrnout do záloh (SQL Database nebo MySQL), a pak kliknout na **OK**.  
   
    ![Výběr účtu úložiště](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Aby se databáze zobrazila v tomto seznamu, musí v části **připojovací řetězce** na stránce **nastavení aplikace** pro vaši aplikaci existovat připojovací řetězec. 
   >
   > Databáze MySQL v aplikaci se automaticky zálohují bez jakýchkoli konfigurací. Pokud ručně nastavíte nastavení pro databáze MySQL v aplikaci, například přidávání připojovacích řetězců, zálohování nemusí správně fungovat.
   > 
   > 
6. Na stránce **Konfigurace zálohování** klikněte na **Uložit**.    
7. Na stránce **zálohy** klikněte na **zálohovat**.
   
    ![Tlačítko BackUpNow][BackUpNow]
   
    Během procesu zálohování se zobrazí zpráva o průběhu.

Po nakonfigurování účtu úložiště a kontejneru můžete kdykoli spustit ruční zálohování.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurace automatizovaných záloh
1. Na stránce **Konfigurace zálohování** nastavte **naplánované zálohování** **na zapnuto**. 
   
    ![Výběr účtu úložiště](./media/web-sites-backup/05ScheduleBackup1.png)
2. Budou se zobrazovat možnosti plánu zálohování, nastavit **naplánované zálohování** na **zapnuto**a pak podle potřeby nakonfigurovat plán zálohování a kliknout na **OK**.
   
    ![Povolit automatizované zálohování][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Konfigurace částečných záloh
Někdy nechcete, aby se všechno ve vaší aplikaci zazálohovali. Tady je pár příkladů:

* [Nastavíte týdenní zálohy](#configure-automated-backups) vaší aplikace, které obsahují statický obsah, který se nikdy nemění, například staré blogové příspěvky nebo obrázky.
* Vaše aplikace má více než 10 GB obsahu (to je maximální množství, které můžete zálohovat v čase).
* Soubory protokolu nechcete zálohovat.

Částečné zálohování vám umožní vybrat, jaké soubory chcete zálohovat.

> [!NOTE]
> Jednotlivé databáze v záloze můžou být 4GB Max, ale celková maximální velikost zálohy je 10 GB.

### <a name="exclude-files-from-your-backup"></a>Vyloučení souborů ze zálohy
Předpokládejme, že máte aplikaci, která obsahuje soubory protokolů a statické bitové kopie, které se zálohují jednou a nebudete je měnit. V takových případech můžete vyloučit tyto složky a soubory z uložení v budoucích zálohách. Pokud chcete z vašich záloh vyloučit soubory a složky, vytvořte `_backup.filter` soubor `D:\home\site\wwwroot` ve složce vaší aplikace. Zadejte seznam souborů a složek, které chcete v tomto souboru vyloučit. 

Snadný způsob, jak přistupovat k souborům, je použít Kudu. Klikněte na **Rozšířené nástroje – > přejít** pro vaši webovou aplikaci, abyste měli přístup k Kudu.

![Kudu pomocí portálu][kudu-portal]

Identifikujte složky, které chcete z vašich záloh vyloučit.  Například chcete vyfiltrovat zvýrazněnou složku a soubory.

![Složka imagí][ImagesFolder]

Vytvořte soubor s názvem `_backup.filter` a vložte předchozí seznam do souboru, ale odeberte. `D:\home` Vypíše jeden adresář nebo soubor na řádek. Obsah souboru by měl být následující:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Nahrajte `_backup.filter` soubor `D:\home\site\wwwroot\` do adresáře vašeho webu pomocí [FTP](deploy-ftp.md) nebo jakékoli jiné metody. Pokud chcete, můžete soubor vytvořit přímo pomocí Kudu `DebugConsole` a vložit do něj obsah.

Zálohování spouštějte stejným způsobem jako obvykle [ručně](#create-a-manual-backup) nebo [automaticky](#configure-automated-backups). Nyní jsou všechny soubory a složky, které jsou zadány v `_backup.filter` , vyloučené z budoucích plánovaných záloh nebo ručně iniciované. 

> [!NOTE]
> Částečné zálohy vaší lokality obnovíte stejným způsobem jako při [pravidelném zálohování](web-sites-restore.md). Proces obnovení dělá správnou věc.
> 
> Po obnovení úplného zálohování se veškerý obsah na webu nahradí jakýmkoli obsahem v záloze. Pokud je soubor v lokalitě, ale ne v záloze, bude odstraněn. Ale při obnovení částečného zálohování zůstane libovolný obsah umístěný v jednom z zakázaných adresářů nebo v jakémkoli zakázaném souboru.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Způsob ukládání záloh
Po provedení jedné nebo více záloh aplikace se zálohy zobrazí na stránce **kontejnery** vašeho účtu úložiště a v aplikaci. V účtu úložiště se každá záloha skládá ze`.zip` souboru, který obsahuje data zálohy, `.xml` a souboru, který `.zip` obsahuje manifest obsahu souboru. Pokud chcete mít přístup k vašim zálohám, aniž byste museli provádět obnovení aplikace, můžete tyto soubory rozbalit a procházet.

Záloha databáze pro aplikaci je uložena v kořenové složce souboru ZIP. V případě databáze SQL se jedná o soubor BACPAC (bez přípony souboru) a je možné ho importovat. Pokud chcete vytvořit databázi SQL založenou na exportu BACPAC, přečtěte si téma [Import souboru BacPac pro vytvoření nové uživatelské databáze](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Změna všech souborů v kontejneru **websitebackups** může způsobit, že se zálohování stane neplatným, a proto není obnovitelné.
> 
> 

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Správu zálohování můžete automatizovat pomocí skriptů pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/overview).

Ukázky najdete v těchto tématech:

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

