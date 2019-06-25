---
title: Migrace starších verzí Azure DNS Private Zones na nový Model prostředků
description: Tato příručka obsahuje podrobné pokyny o tom, jak migrovat starší verze privátní zóny DNS na nejnovější model prostředků
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: e7ebbf35cd572601f02a69930b58811686a92c86
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276094"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrace starších verzí privátními zónami Azure DNS na nový model prostředků

Dodali jsme nový model rozhraní API/prostředků pro privátní zóny Azure DNS jako součást verze preview aktualizace. Aktualizace ve verzi Preview obsahuje nové funkce a odebírá několik omezení a limity počáteční verzi public preview. Mezi tyto výhody nejsou však k dispozici pro privátní zóny DNS, které byly vytvořeny pomocí starší verze rozhraní API. Získáte výhody novou verzi, musí migrovat starší verze zdrojů privátní zóny DNS na nový model prostředků. Proces migrace je jednoduché a poskytujeme skript Powershellu pro automatizaci tohoto procesu. Tato příručka poskytuje návod krok za krokem při migraci na nový model resource privátní zóny Azure DNS.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte nainstalovanou nejnovější verzi Azure powershellu. Další informace o prostředí Azure PowerShell (Az) a jak ji nainstalovat, navštivte https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Ujistěte se, že jste Az.PrivateDns modulu Azure PowerShell nainstalovaný. Chcete-li nainstalovat tento modul, otevřete okno Powershellu se zvýšenými oprávněními (správce režimu) a zadejte následující příkaz

```powershell
Install-Module -Name Az.PrivateDns -AllowPrerelease
```

>[!IMPORTANT]
>Proces migrace je plně automatizované a neočekává se způsobit žádné výpadky. Ale pokud používáte privátními zónami Azure DNS (preview) v důležité produkční prostředí by měl spuštění pod proces migrace během časového intervalu plánované údržby. Ujistěte se, že nebudete muset měnit konfiguraci nebo sady záznamů privátních zón DNS zatímco spouštíte skript migrace.

## <a name="installing-the-script"></a>Instalace skriptu

Otevřete okno Powershellu se zvýšenými oprávněními (režim správce) a spusťte následující příkaz

```powershell
install-script PrivateDnsMigrationScript
```

Zadejte "A" po zobrazení výzvy k instalaci skriptu

![Instalace skriptu](./media/private-dns-migration-guide/install-migration-script.png)

Skript prostředí PowerShell na nejnovější verzi můžete získat také ručně https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

## <a name="running-the-script"></a>Spuštění skriptu

Spusťte následující příkaz pro spuštění skriptu

```powershell
PrivateDnsMigrationScript.ps1
```

![Spuštění skriptu](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Zadejte ID předplatného a přihlášení do Azure

Budete vyzváni k zadání ID předplatného, který obsahuje privátní zóny DNS, které máte v úmyslu migrovat. Budete vyzváni k přihlášení ke svému účtu Azure. Dokončete přihlášení tak, aby skript můžete přístup k soukromým prostředkům zóny DNS v rámci předplatného.

![Přihlášení k Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Vyberte zóny DNS, které chcete migrovat

Skript se získat seznam všech privátních zón DNS v rámci předplatného a výzvu k potvrzení, které chcete migrovat. Zadejte "A" migrovat všechny privátní zóny DNS. Po provedení tohoto kroku skript vytvořit nový privátní zóny DNS pomocí nového modelu prostředků a zkopírovat data do nové zóny DSN. Tento krok se nezmění vaše stávající privátní zóny DNS v přesto.

![Vyberte zóny DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Přepnutí překlad DNS do nové zóny DNS

Jakmile zóny a záznamy byly zkopírovány do nového modelu prostředků, skript vás vyzve k přepnutí překlad DNS do nové zóny DNS. Tento krok se odebere přidružení mezi vaší virtuální sítě a starší verze privátní zóny DNS. Když starší verze zóna je odpojené od virtuální sítí, nové zóny DNS, které jsou vytvořené v nad krok by automaticky převzít kontrolu nad překlad názvů DNS pro tyto virtuální sítě.

Vyberte "A", chcete-li přepnout překlad názvů DNS pro všechny virtuální sítě.

![Přepínání překlad názvů](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Ověřte překlad DNS

Než budete pokračovat, ověřte, že překlad názvů DNS pro vaše zóny DNS funguje podle očekávání. Můžete přihlásíte do virtuálních počítačů azure a problém nslookup dotazu na migrované zón, ověřte, že překlad názvů DNS funguje.

![Ověření překladu názvů](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Pokud zjistíte, že dotazy DNS nejsou řešení, počkejte pár minut a zkuste dotazy. Pokud dotazy DNS fungují podle očekávání, zadejte "Y" skript váz vyzve k odebrání privátní zóny DNS virtuální sítě.

![Ověřte překlad názvů](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Pokud z důvodu DNS z jakéhokoli důvodu rozlišení proti migrované zóny nefunguje podle očekávání, zadejte n v nad krok a skript se přepněte zpět do starší verze zóny překlad DNS. Vytvořit lístek podpory a pomůžeme vám s migrací vaší zóny DNS.

## <a name="cleanup"></a>Vyčištění

Tento krok odstraní starší verze zóny DNS a budou spuštěny pouze po ověření, že překlad názvů DNS funguje podle očekávání. Budete vyzváni k odstranění jednotlivých privátní zóny DNS. Zadejte "Y" na každý řádek po ověření, že překlad názvů DNS pro tuto zóny pracuje správně.

![Vyčištění](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Aktualizace vaší automatizace

Pokud používáte automation, jako jsou šablony, skriptů Powershellu nebo vlastní kód vyvinuté pomocí sady SDK, je nutné aktualizovat automatizace použít nový model prostředků pro privátní zóny DNS. Níže jsou uvedeny odkazy na novou dokumentaci privátní DNS rozhraní příkazového řádku/PS/SDK.
* [Azure DNS privátních zón rozhraní REST API](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Azure DNS privátních zón rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Azure privátními zónami DNS Powershellu](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Azure DNS privátních zón SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Potřebujete další pomoc.

Pokud potřebujete další pomoc s procesem migrace nebo z důvodu z jakéhokoli důvodu výše uvedené kroky nefungují, můžete vytvořte lístek podpory. Zahrňte soubor přepisu generovaný skript prostředí PowerShell pomocí vašeho lístku podpory.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak vytvořit privátní zónu v Azure DNS pomocí [prostředí Azure PowerShell](./private-dns-getstarted-powershell.md) nebo [rozhraní příkazového řádku Azure](./private-dns-getstarted-cli.md).

* Přečtěte si informace o některé běžné [scénáře pro privátní zóny](./private-dns-scenarios.md) , která se dají realizovat s privátními zónami v Azure DNS.

* Pro běžné otázky a odpovědi k privátní zóny v Azure DNS, včetně konkrétní chování můžete očekávat pro některé typy operací, najdete v článku [nejčastější dotazy k DNS privátní](./dns-faq-private.md).

* Další informace o záznamy a zóny DNS najdete [Přehled záznamů a zón DNS](dns-zones-records.md).

* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
