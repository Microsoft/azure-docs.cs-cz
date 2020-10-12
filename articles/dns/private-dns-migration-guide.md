---
title: Migrace starších Azure DNS Private Zones do nového modelu prostředků
titleSuffix: Azure DNS
description: Tato příručka poskytuje podrobné pokyny k migraci starších privátních zón DNS do nejnovějšího modelu prostředků.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: 9302e922d509f3145fd7913835ce896a6da860fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84699460"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrace starších Azure DNS privátních zón do nového modelu prostředků

Během veřejné verze Preview se privátní zóny DNS vytvořily pomocí prostředku "dnszones" s vlastností "zoneType" nastavenou na "Private". Tyto zóny nebudou podporovány po 31. prosinci 2019 a musí být migrovány do modelu prostředků GA, který využívá typ prostředku "privateDnsZones" místo "dnszones". Proces migrace je jednoduchý a poskytujeme PowerShellový skript pro automatizaci tohoto procesu. Tato příručka poskytuje podrobné pokyny k migraci Azure DNS privátních zón do nového modelu prostředků.

Zjištění prostředků dnszones, které vyžadují migraci; v Azure CLI spusťte níže uvedený příkaz.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte nainstalovanou nejnovější verzi Azure PowerShell. Další informace o Azure PowerShell (AZ) a o tom, jak ji nainstalovat, najdete v tématu. https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Ujistěte se, že jste pro Azure PowerShell nainstalovanou modul AZ. PrivateDns. Pokud chcete tento modul nainstalovat, otevřete okno PowerShellu se zvýšenými oprávněními (režim správy) a zadejte následující příkaz.

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Proces migrace je plně automatizovaný a neočekává se, že by došlo k výpadku. Pokud ale v kritickém provozním prostředí používáte Azure DNS privátní zóny (Preview), měli byste při plánovaném časovém intervalu údržby spustit následující postup migrace. Ujistěte se, že při spuštění migračního skriptu neupravujte konfiguraci ani sady záznamů privátních zón DNS.

## <a name="installing-the-script"></a>Instalace skriptu

Otevřete okno PowerShellu se zvýšenými oprávněními (režim správy) a spusťte následující příkaz.

```powershell
install-script PrivateDnsMigrationScript
```

Po zobrazení výzvy k instalaci skriptu zadejte "A".

![Instalace skriptu](./media/private-dns-migration-guide/install-migration-script.png)

Nejnovější verzi skriptu PowerShellu můžete také ručně získat na adrese https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>Migrační skript se nesmí spouštět ve službě Azure Cloud Shell a musí se spustit na VIRTUÁLNÍm počítači nebo místním počítači připojeném k Internetu.

## <a name="running-the-script"></a>Spuštění skriptu

Spusťte následující příkaz, který spustí skript.

```powershell
PrivateDnsMigrationScript.ps1
```

![Spuštění skriptu](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Zadejte ID předplatného a přihlaste se k Azure.

Zobrazí se výzva k zadání ID předplatného obsahujícího privátní zóny DNS, které chcete migrovat. Zobrazí se výzva, abyste se přihlásili ke svému účtu Azure. Dokončete přihlášení, aby skript mohl přistupovat k prostředkům privátní zóny DNS v předplatném.

![Přihlášení k Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Vyberte zóny DNS, které chcete migrovat.

Skript s získá seznam všech privátních zón DNS v rámci předplatného a zobrazí výzvu k potvrzení, které z nich chcete migrovat. Zadejte "A" pro migraci všech privátních zón DNS. Po provedení tohoto kroku skript vytvoří nové privátní zóny DNS pomocí nového modelu prostředků a zkopíruje data do nové zóny DSN. V tomto kroku se zatím nemění vaše stávající privátní zóny DNS.

![Vybrat zóny DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Přepnutí překladu DNS na nové zóny DNS

Po zkopírování zón a záznamů do nového modelu prostředků vás skript vyzve k přepnutí překladu DNS na nové zóny DNS. Tento krok Odebere přidružení mezi staršími privátními zónami DNS a vašimi virtuálními sítěmi. Pokud je starší zóna z virtuálních sítí odpojování, nové zóny DNS vytvořené v rámci výše uvedeného kroku by automaticky převzaly překlad DNS pro tyto virtuální sítě.

Pokud chcete přepínat překlad DNS pro všechny virtuální sítě, vyberte A.

![Přepínání překladu názvů](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Ověření překladu názvů DNS

Než budete pokračovat, ověřte, že překlad DNS na vašich zónách DNS funguje podle očekávání. Můžete se přihlásit k virtuálním počítačům Azure a vystavit dotaz nslookup na migrované zóny, abyste ověřili, že překlad DNS funguje.

![Ověření překladu názvů](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Pokud zjistíte, že dotazy DNS nejsou vyřešeny, počkejte několik minut a opakujte dotaz. Pokud dotazy DNS fungují podle očekávání, zadejte ' Y ', pokud se skript vyzve k odebrání virtuální sítě z privátní zóny DNS.

![Potvrzení překladu názvů](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Pokud z jakéhokoli důvodu překlad DNS na migrované zóny nefunguje podle očekávání, zadejte ' N ' ve výše uvedeném kroku a skript přepne překlad DNS zpátky do zastaralých zón. Vytvořte lístek podpory a můžeme vám pomoci s migrací vašich zón DNS.

## <a name="cleanup"></a>Vyčištění

Tento krok odstraní starší zóny DNS a měl by se provádět až po ověření, že překlad DNS funguje podle očekávání. Zobrazí se výzva k odstranění všech privátních zón DNS. Po ověření správného fungování překladu názvů DNS pro tyto zóny zadejte ' Y ' při každé výzvě.

![Vyčištění](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Aktualizace automatizace

Pokud používáte automatizaci, včetně šablon, skriptů PowerShellu nebo vlastního kódu vyvinutého pomocí sady SDK, musíte službu Automation aktualizovat tak, aby používala nový model prostředků pro privátní zóny DNS. Níže najdete odkazy na novou privátní dokumentaci k DNS CLI/PS/SDK.
* [Azure DNS privátní zóny REST API](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Azure DNS rozhraní příkazového řádku privátních zón](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Prostředí PowerShell pro privátní zóny Azure DNS](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Sada SDK privátních zón Azure DNS](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Potřebujete další pomoc

Pokud potřebujete další pomoc s procesem migrace nebo z jakéhokoli důvodu, že výše uvedené kroky nefungují za vás, vytvořte lístek podpory. Přidejte soubor přepisu generovaný skriptem PowerShellu s vaším lístkem podpory.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak vytvořit privátní zónu v Azure DNS pomocí [Azure PowerShell](./private-dns-getstarted-powershell.md) nebo [Azure CLI](./private-dns-getstarted-cli.md).

* Přečtěte si o některých běžných [scénářích privátní zóny](./private-dns-scenarios.md) , které je možné v Azure DNS realizovat s privátními zónami.

* Běžné otázky a odpovědi týkající se privátních zón v Azure DNS, včetně konkrétního chování, které můžete očekávat u určitých druhů operací, najdete v tématu [privátní DNS Nejčastější dotazy](./dns-faq-private.md).

* Seznamte se s informacemi o zónách a záznamech DNS návštěvou [přehledu zón a záznamů DNS](dns-zones-records.md).

* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
