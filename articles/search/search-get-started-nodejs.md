---
title: Začínáme se službou Azure Search v Node.js | Dokumentace Microsoftu
description: Projděte si sestavení vyhledávací aplikace v hostované cloudové vyhledávací službě v Azure pomocí programovacího jazyka Node.js.
author: jj09
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/26/2017
ms.author: jjed
ms.openlocfilehash: 335781a8048e1497b74f0ecf9bef337c923d95b7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-azure-search-in-nodejs"></a>Začínáme se službou Azure Search v Node.js
> [!div class="op_single_selector"]
> * [Azure Portal](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Naučte se sestavit vlastní vyhledávací aplikaci v Node.js, která k hledání používá službu Azure Search. V tomto kurzu se pomocí [rozhraní REST API služby Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) vytvoří objekty a operace, které se použijí v tomto cvičení.

Tento kód jsme vyvinuli a testovali pomocí [Node.js](https://Nodejs.org) a NPM, [Sublime Text 3](http://www.sublimetext.com/3) a Windows PowerShellu v systému Windows 8.1.

Pokud chcete tuto ukázku spustit, musíte mít službu Azure Search, ke které se můžete zaregistrovat na webu [Azure Portal](https://portal.azure.com). Podrobné pokyny najdete v tématu [Vytvoření služby Azure Search na portálu](search-create-service-portal.md).

## <a name="about-the-data"></a>Informace o datech
Tato ukázková aplikace používá data agentury [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), která jsou filtrovaná pro stát Rhode Island, aby se zmenšila velikost datové sady. Pomocí těchto dat sestavíme vyhledávací aplikaci, která najde významné budovy, například nemocnice a školy, a geologické prvky, jako jsou vodní toky, jezera a vrcholy.

Program **DataIndexer** v této aplikaci sestaví a načte index pomocí konstruktoru [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx), přičemž načte filtrovanou sadu dat USGS z veřejné databáze Azure SQL Database. Přihlašovací údaje a informace o připojení k online zdroji dat jsou uvedené v kódu programu. Není potřeba žádná další konfigurace.

> [!NOTE]
> U této sady dat jsme použili filtr, abychom dodrželi omezení 10 000 dokumentů pro cenovou úroveň Free. Pokud používáte úroveň Standard, toto omezení neplatí. Podrobnosti týkající se kapacity u jednotlivých cenových úrovní najdete v tématu [Omezení služby Search](search-limits-quotas-capacity.md).
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Nalezení názvu služby a klíče API služby Azure Search
Po vytvoření služby se vraťte na portál a získejte adresu URL nebo `api-key`. Připojení k službě Search vyžadují, abyste k ověření volání měli adresu URL i `api-key`.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na panelu odkazů klikněte na **Služba Search** a zobrazte výpis všech služeb Azure Search zřízených pro vaše předplatné.
3. Vyberte službu, kterou chcete použít.
4. Na řídicím panelu služby byste měli vidět dlaždice se základními informacemi, jako například ikonu klíče pro přístup ke klíčům správce.
5. Zkopírujte adresu URL služby, klíč správce a klíč dotazu. Všechny tři položky budete potřebovat později, kdy je přidáte do souboru config.js.

## <a name="download-the-sample-files"></a>Stažení ukázkových souborů
Stáhněte ukázku pomocí libovolného z následujících dvou přístupů.

1. Přejděte na [search-node-indexer-demo](https://github.com/Azure-Samples/search-node-indexer-demo).
2. Klikněte na **Stáhnout ZIP**, uložte soubor .zip a potom z něj extrahujte všechny soubory.

Všechny následné úpravy souborů a spouštěné příkazy se provádí na souborech v této složce.

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Aktualizace souboru config.js. pomocí adresy URL služby Search a klíče rozhraní API
Pomocí adresy URL a klíče rozhraní API, které jste zkopírovali dříve, zadejte do konfiguračního souboru adresu URL, klíč správce a klíč dotazu.

Klíče správce poskytují plnou kontrolu nad operacemi služby, včetně vytvoření nebo odstranění indexu a nahrávání dokumentů. Klíče dotazu oproti tomu slouží k operacím jen pro čtení, které se obvykle používají v klientských aplikacích, které se připojují k službě Azure Search.

Do této ukázky jsme zařadili klíč dotazu, abychom pomohli posílit osvědčený postup, kdy se v klientských aplikacích používá klíč dotazu.

Následující snímek obrazovky ukazuje soubor **config.js** otevřený v textovém editoru – příslušné položky jsou označené, abyste viděli, kde je potřeba soubor aktualizovat pomocí hodnot platných pro vaši vyhledávací službu.

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>Hostování běhového prostředí pro ukázku
Ukázka vyžaduje server HTTP, který můžete nainstalovat globálně pomocí npm.

Pro následující příkazy použijte okno prostředí PowerShell.

1. Přejděte do složky, která obsahuje soubor **package.json**.
2. Zadejte `npm install`.
3. Zadejte `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Sestavení indexu a spuštění aplikace
1. Zadejte `npm run indexDocuments`.
2. Zadejte `npm run build`.
3. Zadejte `npm run start_server`.
4. Nasměrování prohlížeče na adresu `http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Hledání v datech USGS
Sada dat USGS obsahuje záznamy, které se vztahují ke státu Rhode Island. Pokud u prázdného vyhledávacího pole kliknete na tlačítko **Hledat**, obdržíte prvních 50 položek, což je výchozí nastavení.

Když zadáte hledaný výraz, vyhledávací web bude mít s čím pracovat. Zkuste zadat místní název. Roger Williams byl prvním guvernérem státu Rhode Island. Je po něm pojmenovaná celá řada parků, budov a škol.

![][9]

Může taky zkusit kterýkoli z těchto výrazů:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Další postup
Toto je první kurz služby Azure Search založený na Node.js a sadě dat USGS. Postupně ho budeme rozšiřovat o ukázky dalších vyhledávacích funkcí, které by se vám ve vlastních řešeních mohly hodit.

Pokud už službu Azure Search trochu znáte, tato ukázka vám může posloužit jako odrazový můstek k vyzkoušení modulů pro automatické návrhy (našeptávání nebo automatické dokončování dotazů), filtrů a fasetové navigace. Můžete taky zdokonalit stránku výsledků hledání přidáním počtů a dávkováním dokumentů, aby se výsledky daly procházet po stránkách.

Jste nováčky ve službě Azure Search? Doporučujeme vyzkoušet ostatní kurzy a vytvořit si představu o tom, co se dá vytvořit. Pokud hledáte další zdroje, přejděte na [stránku dokumentace](https://azure.microsoft.com/documentation/services/search/). Přístup k dalším informacím vám poskytnou taky odkazy v [Seznamu videí a kurzů](search-video-demo-tutorial-list.md).

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png
