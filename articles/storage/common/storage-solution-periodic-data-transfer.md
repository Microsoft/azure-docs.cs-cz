---
title: Zvolte řešení Azure pro přenos dat pravidelné | Dokumentace Microsoftu
description: Zjistěte, jak zvolit řešení Azure pro přenos dat při přenosu dat pravidelně.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 61d3bcf8fd046ec1b68637bfdcc9827bb5b5d084
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263822"
---
# <a name="solutions-for-periodic-data-transfer"></a>Řešení pro přenos periodických dat.
 
Tento článek poskytuje přehled o přenos dat při přenosu dat pravidelně řešení. Periodických dat. přenos v síti lze označit jako opakovanou v pravidelných intervalech nebo přesun dat nepřetržité. Tento článek také popisuje možnosti převodu doporučené dat a odpovídajících klíčová dovednost matice pro tento scénář.

Přehled o všech pochopit přenos možnosti dostupných dat, přejděte na [volba řešení přenosu dat Azure](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Doporučené možnosti

Doporučené možnosti pro přenos dat pravidelné spadají do dvou kategorií v závislosti na tom, zda je přenos opakovaných nebo průběžné.

- **Skripty a programové nástroje** – pro přenos dat, ke které dochází v pravidelných intervalech, použití skriptované a programové nástroje jako je například AzCopy a Azure Storage – rozhraní REST API. Tyto nástroje jsou zaměřené na IT specialisté a vývojáři.

    - **AzCopy** – snadno kopírovat data do a z Azure BLOB, soubory, pomocí tohoto nástroje příkazového řádku a Table storage poskytovaly optimální výkon. AzCopy podporuje souběžnost a paralelismu a možnost obnovit operace kopírování, když k přerušení.
    - **Azure Storage REST API a sad SDK** – při vytváření aplikace, můžete vyvíjet aplikace proti REST API služby Azure Storage a používat sady SDK Azure nabízí v různých jazycích. Rozhraní REST API můžete taky využít Azure Storage knihovna pro přesun dat určené speciálně pro vysoce výkonné kopírování dat do a z Azure.

- **Nástroje pro souvislá datová ingestování** – pro příjem dat průběžné, probíhající, můžete vybrat jednu z online převodu zařízení Data Box nebo Azure Data Factory. Tyto nástroje jsou nastaveny správcem IT specialisty a transparentně automatizovat datové přenosy.

    - **Azure Data Factory** – Data Factory by měla sloužit k horizontálnímu navýšení kapacity operace přenosu, a pokud je potřeba pro orchestraci a enterprise třída možnosti monitorování. Pomocí služby Azure Data Factory vytvořit kanál cloud, který pravidelně přenos souborů mezi několik služeb Azure, místní nebo kombinaci obojího. Azure Data Factory umožňuje orchestrovat pracovní postupy řízené daty, které ingestovat data z různorodých úložišť dat a automatizace přesunu a transformace dat.
    - **Azure Data Box řady pro online převody** -okraj pole Data a brána pole dat jsou online síťová zařízení, která může přesun dat do a z Azure. Data Box Edge využívá umělou inteligenci (AI)-povoleno hraniční výpočetní předběžně zpracovat data před nahrávání. Brána pole dat je virtuální verzi zařízení pomocí stejné funkce přenosu dat.


## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových funkcí

Následující tabulka shrnuje rozdíly mezi klíčové funkce.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Přenos dat sítě skripty/programátorské

| Schopnost                  | AzCopy                                 | Rozhraní REST API služby Azure Storage       |
|-----------------------------|----------------------------------------|-------------------------------|
| Uspořádání formuláře                 | Nástroj příkazového řádku od Microsoftu       | Vývoj Zákazníci využívající službu Storage <br> Rozhraní REST API pomocí klientské knihovny Azure |
| Počáteční jednorázová nastavení     | Minimální                                | Náročnost vývoje střední, proměnné    |
| Formát dat                 | Objekty BLOB Azure, služba soubory Azure, tabulky Azure | Objekty BLOB Azure, služba soubory Azure, tabulky Azure   |
| Výkon                 | Již optimalizované.                      | Optimalizace při vývoji                  |
| Ceny                     | Bezplatné, vztahují poplatky za odchozí přenos dat      | Bezplatné, vztahují poplatky za odchozí přenos dat        |

### <a name="continuous-data-ingestion-over-network"></a>Příjem průběžné dat přes síť

| Funkce                                       | Brána pole dat (preview) | Data Box Edge (preview)  | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Uspořádání formuláře                                   | Virtuální zařízení             | Fyzické zařízení          | Služby na webu Azure portal, místního agenta                                                            |
| Hardware                                      | Vaše hypervisoru            | Získáte ho od Microsoftu    | Není k dispozici                                                            |
| Počáteční nastavení úsilí                          | Nízká (< 30 min.)            | Střední (~ spárovat hodin) | Velké (~ dnů)                                                 |
| Formát dat                                   | Objekty BLOB Azure, služba soubory Azure   | Objekty BLOB Azure, služba soubory Azure | [Podporuje 70 datové konektory pro úložiště dat a formáty](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Předběžné zpracování dat                           | Ne                         | Ano, prostřednictvím výpočetní Edge    | Ano                                                           |
| Místní mezipaměť<br>(k ukládání dat v místním prostředí)    | Ano                        | Ano                      | Ne                                                            |
| Přenos z ostatních cloudů                    | Ne                         | Ne                       | Ano                                                           |
| Ceny                                       | [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Ceny](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Další postup

- [Přenos dat pomocí AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Další informace o datových přenos pomocí rozhraní API REST úložiště](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
- Pochopit postup:
    - [Přenos dat pomocí brány dat pole](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformace dat pomocí služby Data Box Edge před odesláním do Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Zjistěte, jak přenos dat pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).
