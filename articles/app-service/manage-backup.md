---
title: Zálohování aplikace
description: Přečtěte si, jak vytvářet zálohy aplikací ve službě Azure App Service. Spusťte ruční nebo naplánované zálohy. Přizpůsobte zálohy zahrnutím připojené databáze.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: b812ae10b3462dbeff05c8a67e7ebb725281e7e8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535753"
---
# <a name="back-up-your-app-in-azure"></a>Zálohování aplikace v Azure
Funkce Zálohování a obnovení ve [službě Azure App Service](overview.md) umožňuje snadno vytvářet zálohy aplikací ručně nebo podle plánu. Zálohy můžete nakonfigurovat tak, aby byly zachovány až na dobu neurčitou. Aplikaci můžete obnovit do snímku předchozího stavu přepsáním existující aplikace nebo obnovením do jiné aplikace.

Informace o obnovení aplikace ze zálohy najdete [v tématu Obnovení aplikace v Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Co se zálohuje
Služba App Service můžete zálohovat následující informace do účtu úložiště Azure a kontejneru, který jste nakonfigurovali aplikaci používat. 

* Konfigurace aplikací
* Obsah souboru
* Databáze připojená k vaší aplikaci

Následující databázová řešení jsou podporována funkcí zálohování: 

- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL v aplikaci](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Každá záloha je kompletní offline kopie aplikace, ne přírůstková aktualizace.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Požadavky a omezení
* Funkce Zálohování a obnovení vyžaduje, aby plán služby App Service byl na úrovni **Standard** nebo **Premium.** Další informace o škálování vašeho plánu služby App Service pro použití vyšší vrstvy najdete v [tématu Vertika navýšit kapacitu aplikace v Azure](manage-scale-up.md). **Úroveň Premium** umožňuje větší počet denních záloh než úroveň **Standard.**
* Potřebujete účet úložiště Azure a kontejner ve stejném předplatném jako aplikace, kterou chcete zálohovat. Další informace o účtech úložiště Azure najdete v tématu [Přehled účtu úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).
* Zálohy mohou mít až 10 GB obsahu aplikací a databází. Pokud velikost zálohy překročí tento limit, zobrazí se chyba.
* Zálohy databáze Azure pro MySQL s povolenou tls není podporována. Pokud je záloha nakonfigurována, získáte neúspěšné zálohy.
* Zálohy databáze Azure pro PostgreSQL s povolenou tls není podporováno. Pokud je záloha nakonfigurována, získáte neúspěšné zálohy.
* In-app MySQL databáze jsou automaticky zálohovány bez jakékoli konfigurace. Pokud provedete ruční nastavení pro databáze MySQL v aplikaci, jako je například přidání připojovacích řetězců, zálohy nemusí fungovat správně.
* Použití účtu úložiště s povolenou bránou firewall jako cíle pro zálohování není podporováno. Pokud je záloha nakonfigurována, získáte neúspěšné zálohy.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Vytvoření ruční zálohy
1. Na [webu Azure Portal](https://portal.azure.com)přejděte na stránku aplikace a vyberte **Zálohy**. Zobrazí se stránka **Zálohy.**

    ![Stránka Zálohy](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Pokud se zobrazí následující zpráva, klikněte na ni a upgradujte plán služby App Service, než budete moci pokračovat v zálohování.
    > Další informace najdete [v tématu Vertika navýšit kapacitu aplikace v Azure](manage-scale-up.md).
    > ![Zvolte účet úložiště](./media/manage-backup/upgrade-plan.png)
    > 
    > 

2. Na stránce **Zálohování** vyberte **Zálohování není nakonfigurováno. Chcete-li nakonfigurovat zálohování aplikace, klepněte sem**.

    ![Klikněte na Konfigurovat](./media/manage-backup/configure-start.png)

3. Na stránce **Konfigurace zálohování** klikněte na **Úložiště, které není nakonfigurováno** pro konfiguraci účtu úložiště.

    ![Zvolte účet úložiště](./media/manage-backup/configure-storage.png)

4. Vyberte cíl zálohování výběrem **účtu úložiště** a **kontejneru**. Účet úložiště musí patřit do stejného předplatného jako aplikace, kterou chcete zálohovat. Pokud chcete, můžete si vytvořit nový účet úložiště nebo nový kontejner na příslušných stránkách. Až budete hotovi, klikněte na **Vybrat**.

5. Na stránce **Konfigurace zálohování,** která je stále otevřená, můžete nakonfigurovat **záložní databázi**, pak vyberte databáze, které chcete zahrnout do záloh (databáze SQL nebo MySQL), a klepněte na tlačítko **OK**.

    ![Zvolte účet úložiště](./media/manage-backup/configure-database.png)

    > [!NOTE]
    > Aby se databáze zobrazila v tomto seznamu, musí její připojovací řetězec existovat v části **Připojovací řetězce** na stránce **Nastavení aplikace** pro vaši aplikaci. 
    >
    > In-app MySQL databáze jsou automaticky zálohovány bez jakékoli konfigurace. Pokud provedete ruční nastavení pro databáze MySQL v aplikaci, jako je například přidání připojovacích řetězců, zálohy nemusí fungovat správně.
    > 
    > 

6. Na stránce **Konfigurace zálohování** klepněte na tlačítko **Uložit**.
7. Na stránce **Zálohy** klepněte na tlačítko **Zálohovat**.

    ![Tlačítko BackUpNow](./media/manage-backup/manual-backup.png)

    Během procesu zálohování se zobrazí zpráva o průběhu.

Po konfiguraci účtu úložiště a kontejneru můžete kdykoli zahájit ruční zálohování.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurace automatického zálohování
1. Na stránce **Konfigurace zálohování** nastavte naplánované zálohování na **zapnuto.** **On** 

    ![Povolení automatického zálohování](./media/manage-backup/scheduled-backup.png)

2. Podle potřeby nakonfigurujte plán zálohování a vyberte **ok**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Konfigurace částečných záloh
Někdy nechcete zálohovat všechno v aplikaci. Tady je pár příkladů:

* [Nastavíte týdenní zálohy](#configure-automated-backups) aplikace, která obsahuje statický obsah, který se nikdy nemění, jako jsou staré příspěvky blogu nebo obrázky.
* Vaše aplikace má více než 10 GB obsahu (to je maximální částka, kterou můžete zálohovat najednou).
* Nechcete zálohovat soubory protokolu.

Částečné zálohy umožňují vybrat přesně soubory, které chcete zálohovat.

> [!NOTE]
> Jednotlivé databáze v záloze mohou být maximálně 4 GB, ale celková maximální velikost zálohy je 10 GB

### <a name="exclude-files-from-your-backup"></a>Vyloučení souborů ze zálohy
Předpokládejme, že máte aplikaci, která obsahuje soubory protokolu a statické obrázky, které byly zálohované jednou a nebudou měnit. V takových případech můžete vyloučit tyto složky a soubory z ukládání v budoucích zálohách. Pokud chcete ze záloh vyloučit soubory `_backup.filter` a složky, vytvořte soubor ve `D:\home\site\wwwroot` složce aplikace. Zadejte seznam souborů a složek, které chcete v tomto souboru vyloučit. 

K souborům můžete přistupovat `https://<app-name>.scm.azurewebsites.net/DebugConsole`přechodem na aplikaci . Pokud se zobrazí výzva, přihlaste se ke svému účtu Azure.

Určete složky, které chcete ze záloh vyloučit. Chcete například odfiltrovat zvýrazněnou složku a soubory.

![Složka Obrázky](./media/manage-backup/kudu-images.png)

Vytvořte soubor `_backup.filter` s názvem a vložte předchozí `D:\home`seznam do souboru, ale odeberte . Seznam jednoho adresáře nebo souboru na řádek. Takže obsah souboru by měl být:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Nahrajte `_backup.filter` soubor `D:\home\site\wwwroot\` do adresáře vašeho webu pomocí [ftp](deploy-ftp.md) nebo jiné metody. Pokud si přejete, můžete vytvořit soubor `DebugConsole` přímo pomocí Kudu a vložit obsah tam.

Spouštět zálohy stejným způsobem, jako byste to normálně udělali, [ručně](#create-a-manual-backup) nebo [automaticky](#configure-automated-backups). Nyní jsou všechny soubory a `_backup.filter` složky, které jsou zadány v je vyloučenz budoucích záloh naplánováno nebo ručně zahájeno. 

> [!NOTE]
> Částečné zálohy webu obnovíte stejným způsobem, jako byste [obnovili běžnou zálohu](web-sites-restore.md). Proces obnovení dělá správnou věc.
> 
> Po obnovení úplné zálohy je veškerý obsah na webu nahrazen tím, co je v záloze. Pokud je soubor na webu, ale ne v záloze, bude odstraněn. Ale při obnovení částečné zálohy, veškerý obsah, který je umístěn v jednom z adresářů na černé listině, nebo jakýkoli soubor na černé listině, je ponechán o všem, jak je.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Jak jsou zálohy uloženy
Po vytvoření jedné nebo více záloh pro vaši aplikaci se zálohy zobrazí na stránce **Kontejnery** vašeho účtu úložiště a vaší aplikace. V účtu úložiště se každá záloha skládá ze souboru,`.zip` který obsahuje záložní data, a souboru, `.xml` který obsahuje manifest obsahu souboru. `.zip` Tyto soubory můžete rozbalit a procházet, pokud chcete získat přístup k zálohám, aniž byste skutečně provedli obnovení aplikace.

Záloha databáze pro aplikaci je uložena v kořenovém adresáři souboru ZIP. Pro databázi SQL se jedná o soubor BACPAC (bez přípony souboru) a lze jej importovat. Chcete-li vytvořit databázi SQL na základě exportu BACPAC, přečtěte si [článek Import souboru BACPAC k vytvoření nové databáze uživatelů](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Změna některého ze souborů v kontejneru **backups webů** může způsobit, že záloha se stane neplatnou a proto nelze obnovit.
> 
> 

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Správu zálohování můžete automatizovat pomocí skriptů pomocí [rozhraní Příkazového řádku Azure](/cli/azure/install-azure-cli) nebo Azure [PowerShellu](/powershell/azure/overview).

Ukázky viz:

- [Ukázky Azure CLI](samples-cli.md)
- [Ukázky prostředí Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Další kroky
Informace o obnovení aplikace ze zálohy najdete [v tématu Obnovení aplikace v Azure](web-sites-restore.md). 
