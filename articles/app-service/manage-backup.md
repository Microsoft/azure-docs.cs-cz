---
title: Zálohování aplikace
description: Naučte se vytvářet zálohy vašich aplikací v Azure App Service. Spusťte ruční nebo naplánované zálohy. Přizpůsobte zálohy zahrnutím připojené databáze.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: 0aa0393544f5744c6aaf428ca4b4c27922a0c287
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987840"
---
# <a name="back-up-your-app-in-azure"></a>Zálohování aplikace v Azure
Funkce zálohování a obnovení v [Azure App Service](overview.md) umožňuje snadno vytvářet zálohy aplikací ručně nebo podle plánu. Zálohy můžete nakonfigurovat tak, aby se zachovaly až do neurčitého množství času. Aplikaci můžete obnovit do snímku předchozího stavu přepsáním existující aplikace nebo obnovením do jiné aplikace.

Informace o obnovení aplikace ze zálohy najdete v tématu [obnovení aplikace v Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Co se zálohuje
App Service můžou zálohovat následující informace do účtu služby Azure Storage a kontejneru, který jste nakonfigurovali pro použití. 

* Konfigurace aplikací
* Obsah souboru
* Databáze připojená k vaší aplikaci

Funkce zálohování podporuje následující databázová řešení: 

- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL v aplikaci](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Každá záloha je kompletní offline kopie vaší aplikace, nikoli přírůstková aktualizace.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Požadavky a omezení
* Funkce zálohování a obnovení vyžaduje, aby App Service plán byl v úrovni **Standard**, **Premium** nebo **Isolated** . Další informace o škálování plánu App Service pro použití vyšší úrovně najdete v tématu [horizontální navýšení kapacity aplikace v Azure](manage-scale-up.md). Úrovně **Premium** a **izolované** umožňují větší počet denních zdrojů pro zálohování než úroveň **Standard** .
* Potřebujete účet úložiště Azure a kontejner ve stejném předplatném jako aplikace, kterou chcete zálohovat. Další informace o účtech Azure Storage najdete v tématu [Přehled účtu Azure Storage](../storage/common/storage-account-overview.md).
* Zálohy můžou mít až 10 GB obsahu aplikace a databáze. Pokud velikost zálohy překročí tento limit, zobrazí se chyba.
* Zálohy Azure Database for MySQL s povoleným protokolem TLS nejsou podporovány. Pokud je nakonfigurované zálohování, obdržíte neúspěšné zálohy.
* Zálohy Azure Database for PostgreSQL s povoleným protokolem TLS nejsou podporovány. Pokud je nakonfigurované zálohování, obdržíte neúspěšné zálohy.
* Databáze MySQL v aplikaci se automaticky zálohují bez jakýchkoli konfigurací. Pokud ručně nastavíte nastavení pro databáze MySQL v aplikaci, například přidávání připojovacích řetězců, zálohování nemusí správně fungovat.
* Použití účtu úložiště s povoleným bránou firewall, protože cíl pro zálohování není podporovaný. Pokud je nakonfigurované zálohování, obdržíte neúspěšné zálohy.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Vytvoření ruční zálohy
1. V [Azure Portal](https://portal.azure.com)přejděte na stránku vaší aplikace, vyberte **zálohování**. Zobrazí se stránka **zálohy** .

    ![Stránka zálohy](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Pokud se zobrazí následující zpráva, kliknutím na ni upgradujte plán App Service, abyste mohli pokračovat v zálohování.
    > Další informace najdete v tématu [horizontální navýšení kapacity aplikace v Azure](manage-scale-up.md).
    > :::image type="content" source="./media/manage-backup/upgrade-plan.png" alt-text="Snímek obrazovky s proužkovou zprávou pro upgrade App Service plánu pro přístup k funkci zálohování a obnovení":::
    > 
    > 

2. Na stránce **zálohování** vyberte možnost **zálohování není nakonfigurované. Pokud chcete nakonfigurovat zálohování aplikace, klikněte sem**.

    ![Klikněte na konfigurovat.](./media/manage-backup/configure-start.png)

3. Na stránce **Konfigurace zálohování** klikněte na úložiště, které **není nakonfigurované** , aby se nakonfiguroval účet úložiště.

    :::image type="content" source="./media/manage-backup/configure-storage.png" alt-text="Snímek obrazovky s oddílem úložiště zálohování s vybraným nastavením úložiště nebylo nakonfigurováno.":::

4. Zvolte cíl zálohování a vyberte **účet úložiště** a **kontejner**. Účet úložiště musí patřit do stejného předplatného jako aplikace, kterou chcete zálohovat. Pokud chcete, můžete na příslušných stránkách vytvořit nový účet úložiště nebo nový kontejner. Až skončíte, klikněte na **Vybrat**.

5. Na stránce **Konfigurace zálohování** , která je stále otevřená, můžete nakonfigurovat **záložní databázi**, pak vybrat databáze, které chcete zahrnout do záloh (SQL Database nebo MySQL), a pak kliknout na **OK**.

    :::image type="content" source="./media/manage-backup/configure-database.png" alt-text="Snímek obrazovky oddílu záložní databáze zobrazující zahrnutí do výběru zálohy.":::

    > [!NOTE]
    > Aby se databáze zobrazila v tomto seznamu, musí v části **připojovací řetězce** na stránce **nastavení aplikace** pro vaši aplikaci existovat připojovací řetězec. 
    >
    > Databáze MySQL v aplikaci se automaticky zálohují bez jakýchkoli konfigurací. Pokud ručně nastavíte nastavení pro databáze MySQL v aplikaci, například přidávání připojovacích řetězců, zálohování nemusí správně fungovat.
    > 
    > 

6. Na stránce **Konfigurace zálohování** klikněte na **Uložit**.
7. Na stránce **zálohy** klikněte na **zálohovat**.

    ![Tlačítko BackUpNow](./media/manage-backup/manual-backup.png)

    Během procesu zálohování se zobrazí zpráva o průběhu.

Po nakonfigurování účtu úložiště a kontejneru můžete kdykoli spustit ruční zálohování.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurace automatizovaných záloh
1. Na stránce **Konfigurace zálohování** nastavte **naplánované zálohování** **na zapnuto**. 

    ![Povolit automatizované zálohování](./media/manage-backup/scheduled-backup.png)

2. Podle potřeby nakonfigurujte plán zálohování a vyberte **OK**.

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
Předpokládejme, že máte aplikaci, která obsahuje soubory protokolů a statické bitové kopie, které se zálohují jednou a nebudete je měnit. V takových případech můžete vyloučit tyto složky a soubory z uložení v budoucích zálohách. Pokud chcete z vašich záloh vyloučit soubory a složky, vytvořte `_backup.filter` soubor ve `D:\home\site\wwwroot` složce vaší aplikace. Zadejte seznam souborů a složek, které chcete v tomto souboru vyloučit. 

Přístup k souborům získáte tak, že přejdete na `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Pokud se zobrazí výzva, přihlaste se ke svému účtu Azure.

Identifikujte složky, které chcete z vašich záloh vyloučit. Například chcete vyfiltrovat zvýrazněnou složku a soubory.

![Složka imagí](./media/manage-backup/kudu-images.png)

Vytvořte soubor s názvem `_backup.filter` a vložte předchozí seznam do souboru, ale odeberte `D:\home` . Vypíše jeden adresář nebo soubor na řádek. Obsah souboru by měl být následující:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Nahrajte `_backup.filter` soubor do `D:\home\site\wwwroot\` adresáře vašeho webu pomocí [FTP](deploy-ftp.md) nebo jakékoli jiné metody. Pokud chcete, můžete soubor vytvořit přímo pomocí Kudu `DebugConsole` a vložit do něj obsah.

Zálohování spouštějte stejným způsobem jako obvykle [ručně](#create-a-manual-backup) nebo [automaticky](#configure-automated-backups). Nyní jsou všechny soubory a složky, které jsou zadány v, `_backup.filter` vyloučené z budoucích plánovaných záloh nebo ručně iniciované. 

> [!NOTE]
> Částečné zálohy vaší lokality obnovíte stejným způsobem jako při [pravidelném zálohování](web-sites-restore.md). Proces obnovení dělá správnou věc.
> 
> Po obnovení úplného zálohování se veškerý obsah na webu nahradí jakýmkoli obsahem v záloze. Pokud je soubor v lokalitě, ale ne v záloze, bude odstraněn. Ale při obnovení částečného zálohování zůstane libovolný obsah umístěný v jednom z zakázaných adresářů nebo v jakémkoli zakázaném souboru.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Způsob ukládání záloh
Po provedení jedné nebo více záloh aplikace se zálohy zobrazí na stránce **kontejnery** vašeho účtu úložiště a v aplikaci. V účtu úložiště se každá záloha skládá ze `.zip` souboru, který obsahuje data zálohy, a `.xml` souboru, který obsahuje manifest `.zip` obsahu souboru. Pokud chcete mít přístup k vašim zálohám, aniž byste museli provádět obnovení aplikace, můžete tyto soubory rozbalit a procházet.

Záloha databáze pro aplikaci je uložena v kořenové složce souboru ZIP. V případě SQL Database se jedná o soubor BACPAC (bez přípony souboru) a je možné ho importovat. Pokud chcete vytvořit databázi v Azure SQL Database na základě exportu BACPAC, přečtěte si téma [Import souboru BacPac pro vytvoření databáze v Azure SQL Database](../azure-sql/database/database-import.md).

> [!WARNING]
> Změna všech souborů v kontejneru **websitebackups** může způsobit, že se zálohování stane neplatným, a proto není obnovitelné.
> 
> 

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Správu zálohování můžete automatizovat pomocí skriptů pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/).

Ukázky najdete v těchto tématech:

- [Ukázky Azure CLI](samples-cli.md)
- [Ukázky Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Další kroky
Informace o obnovení aplikace ze zálohy najdete v tématu [obnovení aplikace v Azure](web-sites-restore.md).