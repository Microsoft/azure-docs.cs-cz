---
title: Vytvoření, změna nebo odstranění veřejné IP adresy Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vytvořit, změnit nebo odstranit veřejnou IP adresu.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 38ff40b537730418fe9f0f8295884dae98a2fe0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244911"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Vytvoření, změna nebo odstranění veřejné IP adresy

Přečtěte si o veřejné IP adrese a o tom, jak ji vytvořit, změnit a odstranit. Veřejná IP adresa je prostředek s vlastním konfigurovatelným nastavením. Přiřazení veřejné IP adresy k prostředku Azure, který podporuje veřejné IP adresy, umožňuje:
- Příchozí komunikace z Internetu do prostředku, jako jsou virtuální počítače Azure (VM), aplikační brány Azure, nástroje pro vyrovnávání zatížení Azure, brány Azure VPN a další. Stále můžete komunikovat s některými prostředky, jako jsou virtuální společnosti, z Internetu, pokud virtuální hod nemá přiřazenou veřejnou IP adresu, tak dlouho, dokud je virtuální hod součástí back-endového fondu vyrovnávání zatížení a vyvykladaču zatížení je přiřazena veřejná IP adresa. Pokud chcete zjistit, jestli prostředek pro konkrétní službu Azure může být přiřazen a veřejná IP adresa, nebo jestli ho dá komunikovat prostřednictvím veřejné IP adresy jiného prostředku Azure, přečtěte si dokumentaci ke službě.
- Odchozí připojení k Internetu pomocí předvídatelné ip adresy. Virtuální počítač může například komunikovat odchozí do Internetu bez přiřazené veřejné IP adresy, ale jeho adresa je síťová adresa přeložená Azure na nepředvídatelnou veřejnou adresu ve výchozím nastavení. Přiřazení veřejné IP adresy prostředku umožňuje zjistit, která adresa IP se používá pro odchozí připojení. Ačkoli předvídatelné, adresa se může změnit, v závislosti na zvolené metodě přiřazení. Další informace naleznete [v tématu Vytvoření veřejné IP adresy](#create-a-public-ip-address). Další informace o odchozích připojeních z prostředků Azure najdete v [tématu Principy odchozích připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v libovolné části tohoto článku proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud pomocí příkazů Prostředí PowerShell k dokončení úloh v tomto článku, buď spustit příkazy v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud pomocí příkazů rozhraní Příkazového řádku Azure (CLI) k dokončení úloh v tomto článku, buď spustit příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také `az login` spustit k vytvoření připojení s Azure.

Účet, ke kterému se přihlásíte nebo se s ním připojíte, musí být přiřazen k roli [síťového přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny příslušné akce uvedené v [části Oprávnění](#permissions).

Veřejné IP adresy mají nominální poplatek. Chcete-li zobrazit ceny, přečtěte si stránku [s cenami IP adres.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.
2. Do pole Hledat *na marketplace* zadejte *veřejnou IP adresu.* Když se ve výsledcích hledání zobrazí **veřejná IP adresa,** vyberte ji.
3. V části **Veřejná IP adresa**vyberte **Vytvořit**.
4. V části **Vytvořit veřejnou IP adresu**zadejte nebo vyberte hodnoty pro následující nastavení a pak vyberte **Vytvořit**:

   |Nastavení|Povinné?|Podrobnosti|
   |---|---|---|
   |Verze IP|Ano| Vyberte IPv4 nebo IPv6 nebo Obojí. Výběr emitování obou bude mít za následek vytvoření 2 veřejných IP adres- 1 adresu IPv4 a 1 adresu IPv6. Další informace o [IPv6 v Azure Virtuální počítače](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |Skladová jednotka (SKU)|Ano|Všechny veřejné IP adresy vytvořené před zavedením skladových míst jsou **základní** veřejné IP adresy skladových míst. Po vytvoření veřejné IP adresy nelze skladovou položku změnit. Samostatný virtuální počítač, virtuální počítače v rámci skupiny dostupnosti nebo škálovací sady virtuálních počítačů můžou používat základní nebo standardní sku- su. Míchání skum mezi virtuálními počítači v rámci sad dostupnosti nebo škálovacích sad nebo samostatných virtuálních počítačů není povoleno. **Základní informace** Skladová položka: Pokud vytváříte veřejnou IP adresu v oblasti, která podporuje zóny dostupnosti, je ve výchozím nastavení nastaveno nastavení **zóny dostupnosti** na *hodnotu Žádná.* Základní veřejné IP adresy nepodporují zóny dostupnosti. **Standardní** Skladová položka: K virtuálnímu počítači nebo front-endu nástroje pro vyrovnávání zatížení lze přidružit standardní veřejnou IP adresu SKU. Pokud vytváříte veřejnou IP adresu v oblasti, která podporuje zóny dostupnosti, nastavení **zóny dostupnosti** je ve výchozím nastavení nastaveno na *zónu redundantní.* Další informace o zónách dostupnosti naleznete v nastavení **zóny dostupnosti.** Standardní skladová položka je vyžadována, pokud přidružíte adresu ke standardnímu vykladaču zatížení. Další informace o standardních vyvyřičů zatížení najdete [v tématu Azure vyrovnávání zatížení standardní Skladové položky](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při přiřazování veřejné IP adresy standardní SKU k síťovému rozhraní virtuálního počítače je potřeba explicitně povolit plánovaný provoz pomocí [skupiny zabezpečení sítě](security-overview.md#network-security-groups). Komunikace s prostředkem nebude možná, dokud nevytvoříte a nepřiřadíte skupinu zabezpečení sítě a explicitně nepovolíte požadovaný provoz.|
   |Name (Název)|Ano|Název musí být jedinečný v rámci vybrané skupiny prostředků.|
   |Přiřazení IP adresy|Ano|**Dynamické:** Dynamické adresy se přiřazují jenom po přidružit veřejnou IP adresu k prostředku Azure a prostředek se spustí poprvé. Dynamické adresy se můžou změnit, pokud jsou přiřazeny k prostředku, jako je například virtuální počítač, a virtuální počítač se zastaví (nabude na vysazení) a pak se restartuje. Adresa zůstane stejná, pokud je virtuální počítač restartován nebo zastaven (ale není přidělen). Dynamické adresy jsou uvolněny, když je prostředek veřejné IP adresy oddělen od prostředku, ke který je přidružen. **Statické:** Statické adresy jsou přiřazeny při vytvoření veřejné IP adresy. Statické adresy nejsou uvolněny, dokud není odstraněn prostředek veřejné IP adresy. Pokud adresa není přidružena k prostředku, můžete změnit metodu přiřazení po vytvoření adresy. Pokud je adresa přidružena k prostředku, je možné, že nebude možné změnit metodu přiřazení. Pokud pro **verzi IP**vyberete *protokol IPv6* , musí být metoda přiřazení *Dynamická* pro základní skladovou položku.  Standardní adresy skladové položky jsou *statické* pro IPv4 i IPv6. |
   |Časový limit nečinnosti (minuty)|Ne|Kolik minut chcete zachovat otevřené připojení TCP nebo HTTP bez spoléhání se na klienty, kteří posílají zprávy keep-alive. Pokud pro **verzi IP**vyberete IPv6 , nelze tuto hodnotu změnit. |
   |Popisek názvu DNS|Ne|Musí být jedinečný v umístění Azure, ve které vytvoříte název (ve všech předplatných a všech zákaznících). Azure automaticky zaregistruje název a IP adresu ve svém DNS, takže se můžete připojit k prostředku s názvem. Azure připojí k zadaným názvu výchozí podsíť, například *location.cloudapp.azure.com* (kde je umístění, které vyberete), a vytvoří tak plně kvalifikovaný název DNS. Pokud se rozhodnete vytvořit obě verze adres, bude adresám IPv4 i IPv6 přiřazen stejný název DNS. Výchozí DNS Azure obsahuje záznamy názvů IPv4 A i IPv6 AAAA a reaguje oběma záznamy při vyhlédnout název DNS. Klient zvolí, se kterou adresou (IPv4 nebo IPv6) bude komunikovat. Místo (nebo kromě) použití popisku názvu DNS s výchozí příponou můžete pomocí služby Azure DNS nakonfigurovat název DNS s vlastní příponou, který se přeloží na veřejnou IP adresu. Další informace najdete v tématu věnovaném [použití Azure DNS s veřejnou IP adresou Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Název (Viditelné pouze v případě, že vyberete IP verzi **obou)**|Ano, pokud vyberete ip verzi **obou**|Název se musí lišit od názvu, který zadáte pro první **název** v tomto seznamu. Pokud se rozhodnete vytvořit adresu IPv4 i IPv6, portál vytvoří dva samostatné prostředky veřejné IP adresy, jeden s každou přiřazenou verzí IP adresy.|
   |Přiřazení IP adresy (Viditelné je pouze v případě, že vyberete ip verzi **obou**)|Ano, pokud vyberete ip verzi **obou**|Stejná omezení jako přiřazení IP adresy výše|
   |Předplatné|Ano|Musí existovat ve stejném [předplatném](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) jako prostředek, ke kterému budete přidružit veřejné IP adresy.|
   |Skupina prostředků|Ano|Může existovat ve stejné nebo jiné [skupině prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) jako prostředek, ke kterému budete přidružit veřejné IP adresy.|
   |Umístění|Ano|Musí existovat ve stejném [umístění](https://azure.microsoft.com/regions), označované také jako oblast, jako prostředek, ke kterému budete přidružit veřejné IP adresy.|
   |Zóna dostupnosti| Ne | Toto nastavení se zobrazí pouze v případě, že vyberete podporované umístění. Seznam podporovaných umístění naleznete v tématu [Přehled zón dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud jste vybrali **základní** skladovou položku, *None* vybere se automaticky žádná. Pokud dáváte přednost zaručení určité zóny, můžete vybrat určitou zónu. Obě volby nejsou zónově redundantní. Pokud jste vybrali **standardní** skladovou položku: Zóna redundantní je automaticky vybrána za vás a vaše cesta k datům je odolná vůči selhání zóny. Pokud dáváte přednost zaručit určitou zónu, která není odolná vůči selhání zóny, můžete vybrat určitou zónu.

**Příkazy**

Přestože portál poskytuje možnost vytvořit dva prostředky veřejné IP adresy (jeden IPv4 a jeden IPv6), následující příkazy příkazového řádku a powershellu vytvoří jeden prostředek s adresou pro jednu nebo druhou verzi IP. Pokud chcete dva prostředky veřejné IP adresy, jeden pro každou verzi IP, musíte spustit příkaz dvakrát a zadat různé názvy a verze IP pro prostředky veřejné IP adresy.

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[Nová adresa AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Zobrazení, změna nastavení nebo odstranění veřejné IP adresy

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *veřejnou IP adresu*. Pokud se ve výsledcích hledání zobrazí **veřejné IP adresy,** vyberte je.
2. Vyberte název veřejné IP adresy, kterou chcete zobrazit, změňte nastavení nebo ze seznamu odstraňte.
3. V závislosti na tom, zda chcete zobrazit, odstranit nebo změnit veřejnou IP adresu, dokončete jednu z následujících možností.
   - **Zobrazení**: Část **Přehled** zobrazuje nastavení klíčů pro veřejnou IP adresu, například síťové rozhraní, ke které je přidružena (pokud je adresa přidružena k síťovému rozhraní). Portál nezobrazuje verzi adresy (IPv4 nebo IPv6). Chcete-li zobrazit informace o verzi, použijte příkaz PowerShell nebo CLI k zobrazení veřejné IP adresy. Pokud je verze IP adresy IPv6, přiřazená adresa není zobrazena portálem, prostředím PowerShell nebo CLI.
   - **Odstranit**: Chcete-li odstranit veřejnou ADRESU IP, vyberte **odstranit** v části **Přehled.** Pokud je adresa aktuálně přidružena ke konfiguraci protokolu IP, nelze ji odstranit. Pokud je adresa aktuálně přidružena ke konfiguraci, vyberte **možnost Oddělit,** chcete-li adresu oddělit od konfigurace protokolu IP.
   - **Změnit**: vyberte **Možnost Konfigurace**. Změňte nastavení pomocí informací v kroku 4 [vytvoření veřejné IP adresy](#create-a-public-ip-address). Chcete-li změnit přiřazení adresy IPv4 ze statické na dynamickou, musíte nejprve oddělit veřejnou adresu IPv4 od konfigurace IP adresy, ke které je přidružena. Potom můžete změnit metodu přiřazení na dynamickou a vybrat **přidružit** adresu IP ke stejné konfiguraci IP, jiné konfiguraci nebo ji můžete nechat oddělit. Chcete-li oddělit veřejnou IP adresu, vyberte v části **Přehled** **možnost Oddělit**.

   >[!WARNING]
   >Změníte-li metodu přiřazení ze statické na dynamickou, ztratíte adresu IP, která byla přiřazena k veřejné adrese IP. Zatímco veřejné servery DNS Azure udržují mapování mezi statickými nebo dynamickými adresami a libovolným popiskem názvu DNS (pokud jste ho definovali), dynamická IP adresa se může změnit při spuštění virtuálního počítače po spuštění ve stavu zastavené (vyrovnané). Chcete-li zabránit změně adresy, přiřaďte statickou adresu IP.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az sítě public-ip seznam](/cli/azure/network/public-ip#az-network-public-ip-list) seznam veřejných IP adres, [az sítě public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) ukázat nastavení; [az sítě public-ip aktualizace](/cli/azure/network/public-ip#az-network-public-ip-update) aktualizovat; [az sítě public-ip odstranit](/cli/azure/network/public-ip#az-network-public-ip-delete) odstranit|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) pro načtení objektu veřejné IP adresy a zobrazení jeho nastavení [Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) pro aktualizaci nastavení; [Odstranit-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) odstranit|

## <a name="assign-a-public-ip-address"></a>Přiřazení veřejné IP adresy

Přečtěte si, jak přiřadit veřejnou IP adresu k následujícím zdrojům:

- Virtuální počítač [s Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linuxem](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (při vytváření) nebo na [existující virtuální počítač](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Internetová rovnováha zatížení](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Připojení k webu k webu pomocí brány Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Škálovací sada virtuálních strojů Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Oprávnění

Chcete-li provádět úkoly na veřejných IP adresách, musí být váš účet přiřazen k roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, které jsou přiřazeny příslušné akce uvedené v následující tabulce:

| Akce                                                             | Name (Název)                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAdresy/čtení                           | Čtení veřejné IP adresy                                          |
| Microsoft.Network/publicIPAdresy/zápis                          | Vytvoření nebo aktualizace veřejné IP adresy                           |
| Microsoft.Network/publicIPAdresy/delete                         | Odstranění veřejné IP adresy                                     |
| Microsoft.Network/publicIPAdresy/připojit/akce                    | Přidružení veřejné IP adresy k prostředku                    |

## <a name="next-steps"></a>Další kroky

- Vytvoření veřejné IP adresy pomocí ukázkových skriptů [PowerShellu](powershell-samples.md) nebo [Azure CLI](cli-samples.md) nebo pomocí šablon Azure [Resource Manageru](template-samples.md)
- Vytvoření a použití [zásad Azure](policy-samples.md) pro veřejné IP adresy
