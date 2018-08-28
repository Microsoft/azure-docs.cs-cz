---
title: Začínáme s kolekcemi Pracovních prostorů Microsoft Power BI
description: Kolekce Pracovních prostorů Power BI jsou služba Azure, která vývojářům aplikací umožňuje přidávat interaktivní sestavy Power BI do jejich vlastních aplikací.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: maghan
ms.openlocfilehash: b565955abaae44efe20291a6e46b57a5fec941d1
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046554"
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Začínáme s kolekcemi Pracovních prostorů Microsoft Power BI

**Kolekce Pracovních prostorů Power BI** jsou služba Azure, která vývojářům aplikací umožňuje přidávat interaktivní sestavy Power BI do jejich vlastních aplikací. **Kolekce Pracovních prostorů Power BI** fungují se stávajícími aplikacemi bez nutnosti přepracování nebo změny způsobu, jakým se uživatelé přihlašují.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Prostředky pro **kolekce Pracovních prostorů Microsoft Power BI** se zřizují prostřednictvím [rozhraní API Azure Resource Manageru](https://msdn.microsoft.com/library/mt712306.aspx). V tomto případě je zřízeným prostředkem **kolekce Pracovních prostorů Power BI**.

![Obecný tok kolekcí Pracovních prostorů Power BI](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Vytvoření kolekce pracovních prostorů

**Kolekce pracovních prostorů** je prostředek Azure nejvyšší úrovně a kontejner pro obsah, který se vloží do vaší aplikace. **Kolekci pracovních prostorů** můžete vytvořit dvěma způsoby:

* Ručně na webu Azure Portal
* Programově pomocí rozhraní API Azure Resource Manageru

Projděme si postup pro sestavení **kolekce pracovních prostorů** na webu Azure Portal.

1. Otevřete **Azure Portal** a přihlaste se: [http://portal.azure.com](http://portal.azure.com).
2. Vyberte **+ Nový** na horním panelu.
   
   ![+ Nový na webu Azure Portal](media/get-started/create-workspace-1.png)
3. V části **Data a analýzy** vyberte **Kolekce Pracovních prostorů Power BI**.
4. Pokud již máte existující předplatné kolekce Pracovních prostorů Power BI, v dolní části úvodní zprávy vyberte **Vytvořit kolekci pracovních prostorů**.

5. V části **Kolekce pracovních prostorů** zadejte požadované informace.
   
   ![Vytvoření kolekce pracovních prostorů](media/get-started/create-workspace-2.png)
1. Vyberte **Vytvořit**.

Zřizování **kolekce pracovních prostorů** chvíli trvá. Po dokončení budete přesměrování do **kolekce pracovních prostorů**.

   ![Kolekce pracovních prostorů na webu Azure Portal](media/get-started/create-workspace-3.png)

Výsledky **Vytvoření** obsahují informace potřebné k volání rozhraní API, která slouží k vytváření pracovních prostorů a nasazování obsahu do nich.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Zobrazení přístupových klíčů k rozhraním API pro Power BI

Jednou z nejdůležitějších informací potřebných k volání rozhraní REST API pro Power BI jsou **přístupové klíče**. Na jejich základě se generují **tokeny aplikací** používané k ověřování vašich žádostí o rozhraní API. Pokud chcete **Přístupové klíče** zobrazit, klikněte na **Přístupové klíče** v části **Nastavení**. Další informace o **tokenech aplikací** najdete v tématu [Ověřování a autorizace pomocí kolekcí Pracovních prostorů Power BI](app-token-flow.md).

   ![Přístupové klíče v rámci nastavení kolekce pracovních prostorů na webu Azure Portal](media/get-started/access-keys.png)

Jak si všimnete, máte dva klíče.

   ![Dva klíče v rámci přístupových klíčů](media/get-started/access-keys-2.png)

Zkopírujte tyto klíče a zabezpečeně je uložte do své aplikace. Je důležité, abyste s těmito klíči zacházeli jako s heslem, protože poskytují přístup k veškerému obsahu ve vaší **kolekci pracovních prostorů**.

Ačkoli jsou uvedeny dva klíče, najednou je zapotřebí pouze jeden z nich. Druhý klíč je k dispozici proto, abyste mohli klíče pravidelně obnovovat, aniž by to ovlivnilo přístup ke službě.

Nyní když máte instanci Power BI pro vaši aplikaci a **přístupové klíče**, můžete do vlastní aplikace naimportovat sestavu. Než si řekneme, jak sestavu importovat, dozvíte se v další části, jak vytvářet datové sady a sestavy Power BI určené pro vložení do aplikace.

## <a name="working-with-workspaces"></a>Práce s pracovními prostory

Po vytvoření vaší kolekce pracovních prostorů musíte vytvořit pracovní prostor, který bude obsahovat vaše sestavy a datové sady. Pokud chcete vytvořit pracovní prostor, musíte použít rozhraní [Post Workspace REST API](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Vytváření datových sad a sestav Power BI určených pro vložení do aplikace pomocí Power BI Desktopu

Nyní když jste pro svou aplikaci vytvořili instanci Power BI a máte **přístupové klíče**, musíte si vytvořit datové sady a sestavy Power BI, které do ní chcete vložit. Datové sady a sestavy lze vytvořit pomocí **Power BI Desktopu**. [Power BI Desktop si můžete stáhnout zdarma](https://go.microsoft.com/fwlink/?LinkId=521662). Nebo pokud chcete rychle začít, můžete si stáhnout [ukázkový soubor PBIX prodejní analýzy](http://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> Další informace o použití **Power BI Desktop** najdete v části [Začínáme s Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Se službou **Power BI Desktop** se ke zdroji dat připojíte importem kopie dat do **Power BI Desktop** nebo přímým připojením zdroje dat pomocí **DirectQuery**.

Zde jsou rozdíly mezi **importem** a použitím **DirectQuery**.

| Import | DirectQuery |
| --- | --- |
| Tabulky, sloupce *a data* se naimportují, tedy zkopírují do **Power BI Desktop**. Při práci s vizualizacemi si **Power BI Desktop** dotazem vyžádá kopii dat. Pokud se mají projevit změny, ke kterým došlo v základních datech, musíte provést aktualizaci, tzn. znovu naimportovat celou aktuální datovou sadu. |Do **Power BI Desktop** se naimportují, tedy zkopírují pouze *tabulky a sloupce*. Při práci s vizualizacemi si **Power BI Desktop** dotazem vyžádá základní zdroj dat, což znamená, že se vám vždy zobrazují aktuální data. |

Další informace o připojení ke zdroji dat naleznete v tématu [Připojení ke zdroji dat](connect-datasource.md).

Po uložení práce v **Power BI Desktop** se vytvoří soubor PBIX. Tento soubor obsahuje vaši sestavu. Kromě toho bude při importu dat soubor PBIX obsahovat úplnou datovou sadu. A pokud použijete **DirectQuery**, soubor PBIX bude obsahovat jen schéma datové sady. Soubor PBIX programově nasadíte do pracovního prostoru pomocí [rozhraní API pro import v Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> **Kolekce Pracovních prostorů Power BI** mají další rozhraní API pro změnu serveru a databáze, na které datová sada odkazuje, a nastavení přihlašovacích údajů pro účet služby, které datová sada bude používat pro připojení k databázi. Viz [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) a [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Vytváření datových sad a sestav Power BI pomocí rozhraní API

### <a name="datasets"></a>Datové sady

V rámci kolekcí Pracovních prostorů Power BI můžete vytvářet datové sady pomocí rozhraní REST API. Potom můžete datovou sadu naplnit daty. To umožňuje pracovat s daty bez potřeby mít Power BI Desktop. Další informace najdete v tématu [Post Datasets](https://msdn.microsoft.com/library/azure/mt778875.aspx).

### <a name="reports"></a>Reports

Můžete ve své aplikaci vytvořit sestavu přímo z datové sady pomocí rozhraní API jazyka JavaScript. Další informace najdete v tématu [Vytvoření nové sestavy z datové sady v kolekcích Pracovních prostorů Power BI](create-report-from-dataset.md).

## <a name="see-also"></a>Viz také

[Začínáme s ukázkou](get-started-sample.md)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[Vložení sestavy](embed-report.md)  
[Vytvoření nové sestavy z datové sady v kolekcích Pracovních prostorů Power BI](create-report-from-dataset.md)
[Uložení sestav](save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)

