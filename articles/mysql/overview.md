---
title: Přehled – Azure Database for MySQL
description: Přečtěte si o službě Azure Database for MySQL, službě relačních databází v cloudu Microsoftu na základě verze MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 20b54afdfbb5641def921ffc17b3c38f6650acab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736215"
---
# <a name="what-is-azure-database-for-mysql"></a>Co je Azure Database for MySQL?

Azure Database for MySQL je služba relačních databází v cloudu Microsoftu založená na [verzi MySQL Community Edition](https://www.mysql.com/products/community/) (k dispozici v rámci licence GPLv2) databázovému stroji verze 5,6, 5,7 a 8,0. Azure Database for MySQL má tyto výhody:

- Integrovaná vysoká dostupnost
- Ochrana dat pomocí automatických záloh a obnovení k bodu v čase po dobu až 35 dnů.
- Automatizovaná údržba základního hardwaru, operačního systému a databázového stroje, aby byla služba zabezpečená a aktuálnost.
- Předvídatelný výkon s využitím celkových průběžných plateb.
- Elastické škálování během několika sekund.
- Optimalizace nákladů řídí schopnost zastavit nebo spustit server. 
- Zabezpečení na podnikové úrovni a špičkové dodržování předpisů pro ochranu citlivých dat v klidovém a pohybovém provozu.
- Monitorování a automatizace, které zjednodušují správu a monitorování pro rozsáhlá nasazení.
- Špičkové prostředí podpory v oboru

Tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Díky tomu se můžete zaměřit na rychlý vývoj aplikací a zkrácení doby dodání produktu na trh, namísto vynakládání prostředků a drahocenného času na správu virtuálních počítačů a infrastruktury. Kromě toho můžete pokračovat ve vývoji aplikací pomocí libovolných opensourcových nástrojů a platforem a dodávat je tak rychle a efektivně, jak váš podnik vyžaduje – a to vše, aniž byste se museli učit nové dovednosti.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Azure Database for MySQL koncepční diagram":::

## <a name="deployment-models"></a>Modely nasazení

Azure Database for MySQL, kterou používá služba MySQL Community Edition, je k dispozici ve dvou režimech nasazení:
- Jeden server 
- Flexibilní server (Preview)
  
### <a name="azure-database-for-mysql---single-server"></a>Azure Database for MySQL – jeden server

Azure Database for MySQL jeden server je plně spravovaná databázová služba s minimálními požadavky pro přizpůsobení databáze. Platforma s jednou serverem je navržená tak, aby zpracovávala většinu funkcí správy databáze, jako jsou třeba opravy, zálohování, vysoká dostupnost, zabezpečení s minimální konfigurací a řízením uživatele. Architektura je optimalizovaná pro integrovanou vysokou dostupnost s 99,99% dostupností v jedné zóně dostupnosti. Podporuje komunitní verze MySQL 5,6, 5,7 a 8,0. Tato služba je všeobecně dostupná pro celou řadu [oblastí Azure](https://azure.microsoft.com/global-infrastructure/services/).

Nasazení typu Jeden server má tři cenové úrovně: Basic, Pro obecné účely a Optimalizováno pro paměť. Každá úroveň nabízí různé schopnosti prostředků pro podporu vašich úloh databáze. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. Podrobnosti najdete v tématu [Cenové úrovně](concepts-pricing-tiers.md).

Pro cloudové nativní aplikace navržené pro zpracování automatizovaných oprav bez nutnosti podrobného řízení plánu oprav a vlastních nastavení konfigurace MySQL se nejlépe hodí samostatné servery. 

Podrobný přehled režimu nasazení na jeden server najdete v tématu [Přehled jediného serveru](single-server-overview.md).

### <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL – flexibilní Server (Preview)

Azure Database for MySQL flexibilní Server je plně spravovaná databázová služba navržená tak, aby poskytovala přesnější kontrolu a flexibilitu nad funkcemi správy databáze a nastaveními konfigurace. Obecně platí, že služba poskytuje větší flexibilitu a přizpůsobení podle požadavků uživatelů. Flexibilní architektura serveru umožňuje uživatelům, aby se mohli rozhodnout o vysoké dostupnosti v rámci jedné zóny dostupnosti a napříč několika zónami dostupnosti. Flexibilní servery poskytují lepší ovládací prvky pro optimalizaci nákladů, díky kterým je možné zastavit nebo spustit server a provést rozstupnou výpočetní vrstvu, která je ideální pro úlohy, které nepotřebují nepřetržitě plný výpočet kapacity. Služba podporuje komunitní verze MySQL 5,7 a 8,0. Služba je momentálně ve verzi Public Preview, která je dnes k dispozici v nejrůznějších [oblastech Azure](https://azure.microsoft.com/global-infrastructure/services/).

Flexibilní servery jsou vhodné pro 
- Vývoj aplikací, který vyžaduje lepší kontrolu a přizpůsobení.
- Redundantní vysoká dostupnost zóny
- Spravovaná okna údržby

Podrobný přehled režimu nasazení flexibilního serveru najdete v tématu [flexibilní přehled serveru](flexible-server/overview.md).

## <a name="contacts"></a>Kontakty
Pokud máte jakékoli dotazy nebo návrhy, které byste mohli potřebovat při práci s Azure Database for MySQL, pošlete e-mail týmu Azure Database for MySQL ([ @Ask Azure DB pro MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Tato e-mailová adresa není alias technické podpory.

Můžete také využít tyto komunikační kanály:

- Pro podporu Azure [vytvořte lístek podpory na webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o těchto dvou režimech nasazení pro Azure Database for MySQL a vyberte si správné možnosti podle svých potřeb.

- [Jeden server](single-server/index.yml)
- [Flexibilní Server](flexible-server/index.yml)
- [Výběr správné možnosti nasazení MySQL pro vaše úlohy](select-right-deployment-type.md)
