---
title: Migrace starších privátních zón Azure DNS do nového modelu prostředků
titleSuffix: Azure DNS
description: Tato příručka obsahuje podrobné pokyny o migraci starších privátních zón DNS do nejnovějšího modelu prostředků
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: d29885104d6f39a17b5bdeb786cda8f56f58d987
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76939353"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrace starších privátních zón Azure DNS do nového modelu prostředků

Během veřejné ho náhledu byly vytvořeny privátní zóny DNS pomocí prostředku dnszones s vlastností zoneType nastavenou na "Soukromé". Tyto zóny nebudou podporovány po 31 prosinci 2019 a musí být migrovány do modelu prostředků GA, který využívá typ prostředku "privateDnsZones" namísto "dnszones". Proces migrace je jednoduchý a my jsme poskytli skript prostředí PowerShell pro automatizaci tohoto procesu. Tato příručka obsahuje podrobné instrukce k migraci privátních zón Azure DNS do nového modelu prostředků.

Chcete-li zjistit dnszones prostředky, které vyžadují migraci; proveďte níže uvedený příkaz v azure cli.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte nainstalovanou nejnovější verzi Azure PowerShellu. Další informace o Azure PowerShellu (Az) a o tom, jak ho nainstalovat, najdete nahttps://docs.microsoft.com/powershell/azure/new-azureps-module-az

Ujistěte se, že máte nainstalovaný modul Az.PrivateDns pro Azure PowerShell. Chcete-li nainstalovat tento modul, otevřete okno powershellu se zvýšenými oprávněními (režim správy) a zadejte následující příkaz.

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Proces migrace je plně automatizovaný a neočekává se, že by způsoboval žádné prostoje. Pokud však používáte privátní zóny Azure DNS (preview) v kritickém produkčním prostředí, měli byste během plánovaného časového období údržby provést následující proces migrace. Při spuštění skriptu pro migraci se ujistěte, že neupravujete konfiguraci nebo sady záznamů privátních zón DNS.

## <a name="installing-the-script"></a>Instalace skriptu

Otevření okna prostředí PowerShell se zvýšenými oprávněními (režim správy) a spuštění následujícího příkazu

```powershell
install-script PrivateDnsMigrationScript
```

Po zobrazení výzvy k instalaci skriptu zadejte "A".

![Instalace skriptu](./media/private-dns-migration-guide/install-migration-script.png)

Můžete také ručně získat nejnovější verzi skriptu prostředí PowerShell na adresehttps://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>Skript migrace se nesmí spouštět v cloudovém prostředí Azure a musí být spuštěn ve virtuálním počítači nebo místním počítači připojeném k internetu.

## <a name="running-the-script"></a>Spuštění skriptu

Spuštění následujícího příkazu pro spuštění skriptu

```powershell
PrivateDnsMigrationScript.ps1
```

![Spuštění skriptu](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Zadejte ID předplatného a přihlášení k Azure

Budete vyzváni k zadání ID předplatného obsahujícího privátní zóny DNS, které chcete migrovat. Budete vyzváni k přihlášení ke svému účtu Azure. Dokončete přihlášení, aby skript měl přístup k prostředkům privátní zóny DNS v předplatném.

![Přihlášení k Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Vyberte zóny DNS, které chcete migrovat.

Skript s získat seznam všech soukromých zón DNS v předplatném a výzvu k potvrzení, které z nich chcete migrovat. Zadáním "A" migrujete všechny soukromé zóny DNS. Po provedení tohoto kroku skript vytvoří nové privátní zóny DNS pomocí nového modelu prostředků a zkopíruje data do nové zóny DSN. Tento krok nezmění stávající privátní zóny DNS v každém případě.

![Vybrat zóny DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Přepnutí překladu DNS na nové zóny DNS

Po zkopírování zón a záznamů do nového modelu prostředků vás skript vyzve k přepnutí překladu DNS do nových zón DNS. Tento krok odebere přidružení mezi staršími soukromými zónami DNS a virtuálními sítěmi. Pokud je starší zóna odpojena od virtuálních sítí, nové zóny DNS vytvořené ve výše uvedeném kroku automaticky převezmou překlad DNS pro tyto virtuální sítě.

Chcete-li přepnout rozlišení DNS pro všechny virtuální sítě, vyberte možnost A.

![Přepínání překladu názvů](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Ověření rozlišení DNS

Než budete pokračovat, ověřte, zda rozlišení DNS v zónách DNS funguje podle očekávání. Můžete se přihlásit k virtuálním počítačům azure a vydat dotaz nslookup proti migrované zóny a ověřit, že rozlišení DNS funguje.

![Ověřit překlad názvů](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Pokud zjistíte, že dotazy DNS nejsou řešení, počkejte několik minut a opakujte dotazy. Pokud dotazy DNS fungují podle očekávání, zadejte "Y", když vás skript vyzve k odebrání virtuální sítě ze soukromé zóny DNS.

![Potvrdit překlad názvů](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Pokud z nějakého důvodu nefunguje rozlišení DNS proti migrovaných zónám očekávaným způsobem, zadejte "N" ve výše uvedeném kroku a skript přepne rozlišení DNS zpět na starší zóny. Vytvořte lístek podpory a my vám pomůžeme s migrací vašich DNS zón.

## <a name="cleanup"></a>Vyčištění

Tento krok odstraní starší zóny DNS a měl by být proveden až po ověření, že rozlišení DNS funguje podle očekávání. Budete vyzváni k odstranění každé soukromé zóny DNS. Po ověření správného fungování překladu DNS pro tyto zóny zadejte "Y" na každé rychlé výzvě.

![Vyčištění](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Aktualizujte automatizaci

Pokud používáte automatizaci včetně šablon, skriptů prostředí PowerShell nebo vlastního kódu vyvinutého pomocí sady SDK, je nutné aktualizovat automatizaci tak, aby používala nový model prostředků pro privátní zóny DNS. Níže jsou uvedeny odkazy na nové soukromé DNS CLI / PS / SDK dokumentace.
* [Rozhraní REST PRI Z AZURE DNS](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Cli prizonátních zón Azure DNS](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Privátní zóny Azure DNS PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Privátní zóny Azure SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Potřebujete další pomoc

Vytvořte lístek podpory, pokud potřebujete další pomoc s procesem migrace nebo z jakéhokoli důvodu výše uvedené kroky nefungují pro vás. Zahrňte soubor přepisu generovaný skriptem prostředí PowerShell s lístkem podpory.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak vytvořit privátní zónu ve Službě Azure DNS pomocí [Azure PowerShellu](./private-dns-getstarted-powershell.md) nebo [Azure CLI](./private-dns-getstarted-cli.md).

* Přečtěte si o některých [běžných scénářích privátní zóny,](./private-dns-scenarios.md) které se můžou realizovat pomocí privátních zón v Azure DNS.

* Běžné otázky a odpovědi týkající se privátních zón ve službě Azure DNS, včetně konkrétního chování, které můžete očekávat u určitých druhů operací, najdete v [tématu Nejčastější dotazy k privátním DNS](./dns-faq-private.md).

* Informace o zónách a záznamech DNS naleznete v přehledu [zón DNS a záznamů](dns-zones-records.md).

* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
