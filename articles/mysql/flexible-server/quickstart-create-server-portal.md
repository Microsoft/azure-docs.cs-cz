---
title: 'Rychlý Start: vytvoření služby Azure DB for MySQL Flexible Server – Azure Portal'
description: Tento článek vás provede postupem použití Azure Portal k rychlému vytvoření Azure Database for MySQL flexibilního serveru během několika minut.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/29/2020
ms.openlocfilehash: 70f2cf183a9bd93b6066516cb68e99ee21cdc1ac
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569620"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Rychlý Start: použití Azure Portal k vytvoření Azure Database for MySQL flexibilního serveru

Azure Database for MySQL flexibilní Server je spravovaná služba, která slouží ke spouštění, správě a škálování vysoce dostupných serverů MySQL v cloudu. V tomto rychlém startu se dozvíte, jak vytvořit flexibilní Server v několika použití Azure Portal.

> [!IMPORTANT] 
> Azure Database for MySQL flexibilní Server je momentálně ve verzi Public Preview.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Otevřete webový prohlížeč a přejděte na web [Azure Portal](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Vytvoření Azure Database for MySQL flexibilního serveru

Vytvoříte flexibilní Server s definovanou sadou [výpočetních prostředků a prostředků úložiště](./concepts-compute-storage.md). Server vytvoříte v rámci [skupiny prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).

Při vytváření flexibilního serveru postupujte podle těchto kroků:

1. V levém horním rohu portálu vyberte **vytvořit prostředek** (+).

2. Vyberte **databáze**  >  **Azure Database for MySQL**. Službu můžete vyhledat také zadáním **MySQL** do vyhledávacího pole.

    > :::image type="content" source="./media/quickstart-create-server-portal/navigate-to-mysql.png" alt-text="Možnost Azure Database for MySQL":::

3. Jako možnost nasazení vyberte **flexibilní Server** .
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Možnost Azure Database for MySQL":::    

4. Ve formuláři **základy** vyplňte následující informace: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Možnost Azure Database for MySQL"::: 
                                    
    |**Nastavení**|**Navrhovaná hodnota**|**Popis**|
    |---|---|---|
    Předplatné|Název vašeho předplatného|Předplatné Azure, které chcete použít pro váš server. Pokud máte více předplatných, vyberte předplatné, ve kterém se má prostředek účtovat.|
    Skupina prostředků|*myresourcegroup*| Název nové skupiny prostředků nebo některé ze stávajících ve vašem předplatném.|
    Název serveru |*mydemoserver*|Jedinečný název, který identifikuje flexibilní Server. Název domény *MySQL.Database.Azure.com* se připojí k názvu serveru, který zadáte. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat z 3 až 63 znaků.|
    Uživatelské jméno správce |*mydemouser*| Váš vlastní přihlašovací účet, který budete používat pro připojení k serveru. Přihlašovací jméno správce nemůže být **azure_superuser**, **admin**, **Administrator**, **root**, **Guest**ani **Public**.|
    Heslo |Vaše heslo| Nové heslo pro účet správce serveru. Musí mít 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice (0–9) a jiné než alfanumerické znaky (!, $, #, % apod.).|
    Region (Oblast)|Oblast nejbližší vašim uživatelům| Umístění co nejblíže vašim uživatelům.|
    Verze|5.7| Hlavní verze MySQL.|
    Výpočty + úložiště | **Shluky**, **Standard_B1ms**, **10 GIB**, **7 dní** | Konfigurace výpočtů, úložiště a zálohování pro nový server. Vyberte **Konfigurovat Server**. *Burstable*Mezi výchozími hodnotami pro **výpočetní vrstvu**, **výpočetní velikost**, **úložiště**a **dobu uchovávání záloh**jsou výchozí hodnoty pro *Standard_B1ms*, *10 GIB*a *7 dní* . Tyto posuvníky můžete ponechat beze změny nebo je upravit. Pokud chcete uložit tento výběr výpočtů a úložišť, vyberte **Uložit** a pokračujte v konfiguracích. Následující snímek obrazovky ukazuje možnosti výpočtů a úložiště.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Možnost Azure Database for MySQL":::

5. Konfigurace možností sítě

    Na kartě sítě si můžete vybrat, jak je server dostupný. Azure Database for MySQL flexibilní Server nabízí dvě možnosti, jak se připojit k serveru prostřednictvím *veřejného přístupu (povolených IP adres)* a *privátního přístupu (integrace virtuální sítě)*. Díky *veřejnému přístupu (povoleným IP adresám)* je přístup k serveru omezený na povolené IP adresy přidané do pravidla brány firewall. Zabraňuje externím aplikacím a nástrojům v připojení k serveru a libovolné databázi na serveru, pokud nevytvoříte pravidlo pro otevření brány firewall pro konkrétní IP adresu nebo rozsah. Pomocí *privátního přístupu (Integration VNET)* je přístup k vašemu serveru omezený na vaši virtuální síť. V tomto rychlém startu vám ukážeme, jak povolit veřejný přístup pro připojení k serveru. Další informace o metodách připojení najdete v [článku koncepty](./concepts-networking.md).

    > [!NOTE]
    > Po vytvoření serveru se metoda připojení nedá změnit. Pokud jste například při vytváření vybrali *veřejný přístup (povolené IP adresy)* , nemůžete po vytvoření změnit na *privátní přístup (Integration VNET)* . Důrazně doporučujeme vytvořit server s privátním přístupem k zabezpečenému přístupu k serveru pomocí integrace virtuální sítě. Přečtěte si další informace o privátním přístupu v [článku koncepty](./concepts-networking.md).

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Možnost Azure Database for MySQL":::  

6. Vyberte možnost **zkontrolovat + vytvořit** a zkontrolujte flexibilní konfiguraci serveru.

7. Vyberte **Vytvořit**, aby se server zřídil. Zřizování může trvat několik minut.

8. Pokud chcete monitorovat proces nasazení, vyberte **Oznámení** (ikona zvonku) na panelu nástrojů. Po dokončení nasazení můžete vybrat **Připnout na řídicí panel**a vytvořit dlaždici pro tento flexibilní Server na řídicím panelu Azure Portal jako zástupce na stránku **Přehled** serveru. Výběr **Přejít k prostředku** otevře stránku **Přehled** serveru.

Ve výchozím nastavení se v rámci vašeho serveru vytvoří následující databáze: **information_schema**, **mysql**, **performance_schema** a **sys**.

> [!NOTE]
> Ověřte, jestli vaše síť umožňuje odchozí provoz přes port 3306, který používá Azure Database for MySQL flexibilní Server, aby se předešlo problémům s připojením.  

## <a name="connect-to-using-mysql-command-line-client"></a>Připojení k pomocí klienta příkazového řádku MySQL

Pokud jste vytvořili flexibilní Server s *privátním přístupem (Integration VNET)*, budete se muset připojit k serveru z prostředku ve stejné virtuální síti jako váš server. Můžete vytvořit virtuální počítač a přidat ho do virtuální sítě vytvořené pomocí flexibilního serveru.

Pokud jste vytvořili flexibilní Server s *veřejným přístupem (povolenými IP adresami)*, můžete přidat místní IP adresu do seznamu pravidel brány firewall na serveru.

Můžete zvolit [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) nebo [MySQL Workbench](./connect-workbench.md) pro připojení k serveru z místního prostředí. 

Pomocí mysql.exe se připojte pomocí níže uvedeného příkazu. Nahraďte hodnoty skutečným názvem serveru a heslem. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Vyčištění prostředků
Úspěšně jste vytvořili Azure Database for MySQL flexibilního serveru ve skupině prostředků.  Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, můžete je odstranit odstraněním skupiny prostředků nebo pouhým odstraněním serveru MySQL. Pokud chcete odstranit skupinu prostředků, postupujte podle těchto kroků:

1. V Azure Portal vyhledejte a vyberte **skupiny prostředků**.
1. V seznamu Skupina prostředků vyberte název vaší skupiny prostředků.
1. Na stránce Přehled vaší skupiny prostředků vyberte **Odstranit skupinu prostředků**.
1. V potvrzovacím dialogovém okně zadejte název vaší skupiny prostředků a pak vyberte **Odstranit**.

Pokud chcete odstranit server, můžete kliknout na tlačítko **Odstranit** na stránce **Přehled** vašeho serveru, jak je znázorněno níže:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Možnost Azure Database for MySQL":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace PHP (Laravel) pomocí MySQL](tutorial-php-database-app.md)
