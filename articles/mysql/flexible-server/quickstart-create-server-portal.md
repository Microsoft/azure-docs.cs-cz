---
title: 'Rychlý Start: vytvoření Azure Database for MySQL flexibilního serveru – Azure Portal'
description: Tento článek vás provede použitím Azure Portal k vytvoření Azure Database for MySQL flexibilního serveru během několika minut.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 864152d1f1d0074305cbba448946bc05888b4f3b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566754"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Rychlý Start: použití Azure Portal k vytvoření Azure Database for MySQL flexibilního serveru

Azure Database for MySQL flexibilní Server je spravovaná služba, kterou můžete použít ke spouštění, správě a škálování vysoce dostupných serverů MySQL v cloudu. V tomto rychlém startu se dozvíte, jak vytvořit flexibilní Server pomocí Azure Portal.

> [!IMPORTANT] 
> Azure Database for MySQL flexibilní Server je momentálně ve verzi Public Preview.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přejděte na web [Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Vytvoření Azure Database for MySQL flexibilního serveru

Vytvoříte flexibilní Server s definovanou sadou [výpočetních prostředků a prostředků úložiště](./concepts-compute-storage.md). Server vytvoříte v rámci [skupiny prostředků Azure](../../azure-resource-manager/management/overview.md).

K vytvoření flexibilního serveru proveďte tyto kroky:

1. Vyhledejte a vyberte **Azure Database for MySQL servery** na portálu:
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Snímek obrazovky, který zobrazuje hledání Azure Database for MySQLch serverů.":::

2. Vyberte **Přidat**. 

3. Na stránce **vybrat možnost nasazení Azure Database for MySQL** jako možnost nasazení vyberte **flexibilní Server** :
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Snímek obrazovky znázorňující možnost flexibilního serveru":::    

4. Na kartě **Základní informace** zadejte následující údaje: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Snímek obrazovky zobrazující kartu základy stránky flexibilního serveru"::: 
                                    
    |**Nastavení**|**Navrhovaná hodnota**|**Popis**|
    |---|---|---|
    Předplatné|Název vašeho předplatného|Předplatné Azure, které chcete použít pro váš server. Pokud máte více předplatných, vyberte předplatné, ve kterém se má prostředek fakturovat.|
    Skupina prostředků|**myresourcegroup**| Název nové skupiny prostředků nebo některé ze stávajících ve vašem předplatném.|
    Název serveru |**mydemoserver**|Jedinečný název, který identifikuje flexibilní Server. Název domény `mysql.database.azure.com` se připojí k názvu serveru, který zadáte. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí mít 3 až 63 znaků.|
    Uživatelské jméno správce |**mydemouser**| Vlastní přihlašovací účet, který se má použít, když se připojujete k serveru. Uživatelské jméno správce nemůže být **azure_superuser** , **admin** , **Administrator** , **root** , **Guest** ani **Public**.|
    Heslo |Vaše heslo| Nové heslo pro účet správce serveru. Musí mít 8 až 128 znaků. Musí také obsahovat znaky ze tří z následujících kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice (0 – 9) a jiné než alfanumerické znaky (!, $, #,% atd.).|
    Oblast|Oblast nejbližší vašim uživatelům| Umístění co nejblíže vašim uživatelům.|
    Verze|**5.7**| Hlavní verze MySQL.|
    Výpočty + úložiště | **Shluky** , **Standard_B1ms** , **10 GIB** , **7 dní** | Konfigurace výpočtů, úložiště a zálohování pro nový server. Vyberte **Konfigurovat Server**. **Burstable** Pro **výpočetní vrstvu** , **výpočetní velikost** , **velikost úložiště** a **dobu uchovávání** záloh jsou výchozí hodnoty pro **Standard_B1ms** , **10 GIB** a **7 dní** . Tyto hodnoty můžete ponechat beze změny nebo je upravit. Pokud chcete uložit výběr výpočtů a úložišť, vyberte **Uložit** a pokračujte v konfiguraci. Následující snímek obrazovky ukazuje možnosti výpočtů a úložiště.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Snímek obrazovky zobrazující možnosti výpočtů a úložiště":::

5. Nakonfigurujte možnosti sítě.

    Na kartě **sítě** si můžete vybrat, jak je server dostupný. Azure Database for MySQL flexibilní Server nabízí dva způsoby, jak se připojit k serveru: 
   - Veřejný přístup (povolené IP adresy)
   - Privátní přístup (integrace virtuální sítě) 
   
   Když použijete veřejný přístup, přístup k serveru je omezený na povolené IP adresy, které přidáte do pravidla brány firewall. Tato metoda zabraňuje externím aplikacím a nástrojům v připojení k serveru a libovolné databázi na serveru, pokud nevytvoříte pravidlo pro otevření brány firewall pro konkrétní IP adresu nebo rozsah. Pokud používáte privátní přístup (Integration VNet), přístup k serveru je omezen na vaši virtuální síť. V tomto rychlém startu se dozvíte, jak povolit veřejný přístup pro připojení k serveru. [Další informace o metodách připojení najdete v článku koncepty.](./concepts-networking.md)

    > [!NOTE]
    > Po vytvoření serveru nemůžete změnit metodu připojení. Pokud třeba při vytváření serveru vyberete **veřejný přístup (s povolenými IP adresami)** , nemůžete po vytvoření serveru změnit na **privátní přístup (Integration VNET)** . Důrazně doporučujeme vytvořit server s privátním přístupem, který vám umožní zabezpečit přístup k serveru prostřednictvím integrace virtuální sítě. [Přečtěte si další informace o privátním přístupu v článku koncepty.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Snímek obrazovky, který zobrazuje kartu síť.":::  

6. Vyberte možnost **zkontrolovat + vytvořit** a zkontrolujte flexibilní konfiguraci serveru.

7. Vyberte **Vytvořit** , aby se server zřídil. Zřizování může trvat několik minut.

8. Pokud chcete monitorovat proces nasazení, vyberte **oznámení** na panelu nástrojů (tlačítko zvonku). Po dokončení nasazení můžete vybrat **Připnout na řídicí panel** a vytvořit dlaždici pro flexibilní Server na řídicím panelu Azure Portal. Tato dlaždice je zástupce na stránku **Přehled** serveru. Když vyberete **Přejít k prostředku** , otevře se stránka **Přehled** serveru.

Ve výchozím nastavení se tyto databáze vytvoří v rámci vašeho serveru: information_schema, MySQL, performance_schema a sys.

> [!NOTE]
> Chcete-li se vyhnout problémům s připojením, ověřte, zda vaše síť umožňuje odchozí provoz přes port 3306, který je používán Azure Database for MySQLm flexibilním serverem.  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Připojení k serveru pomocí mysql.exe

Pokud jste vytvořili flexibilní Server pomocí privátního přístupu (Integration VNet), budete se muset připojit k serveru z prostředku ve stejné virtuální síti jako váš server. Můžete vytvořit virtuální počítač a přidat ho do virtuální sítě vytvořené pomocí flexibilního serveru.

Pokud jste vytvořili flexibilní Server pomocí veřejného přístupu (povolených IP adres), můžete přidat místní IP adresu do seznamu pravidel brány firewall na serveru.

K připojení serveru z místního prostředí můžete použít buď [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) , nebo [MySQL Workbench](./connect-workbench.md) . 

Pokud používáte mysql.exe, připojte se pomocí následujícího příkazu. V příkazu použijte název svého serveru, uživatelské jméno a heslo. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Vyčištění prostředků
Nyní jste vytvořili Azure Database for MySQL flexibilního serveru ve skupině prostředků. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, můžete je odstranit odstraněním skupiny prostředků, nebo můžete jenom odstranit server MySQL. Pokud chcete odstranit skupinu prostředků, proveďte tyto kroky:

1. V Azure Portal vyhledejte a vyberte **skupiny prostředků**.
1. V seznamu skupin prostředků vyberte název vaší skupiny prostředků.
1. Na stránce **Přehled** pro skupinu prostředků vyberte **Odstranit skupinu prostředků**.
1. V potvrzovacím dialogovém okně zadejte název vaší skupiny prostředků a pak vyberte **Odstranit**.

Chcete-li odstranit server, můžete vybrat možnost **Odstranit** na stránce **Přehled** pro váš server, jak je znázorněno zde:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Snímek obrazovky, který ukazuje, jak odstranit server":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace PHP (Laravel) pomocí MySQL](tutorial-php-database-app.md)