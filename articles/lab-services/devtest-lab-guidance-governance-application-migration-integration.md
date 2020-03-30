---
title: Migrace a integrace aplikací v azure devtest labs
description: Tento článek obsahuje pokyny pro zásady správného řízení infrastruktury Azure DevTest Labs v kontextu migrace a integrace aplikací.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 14641e9096fa9366334e9f7460ae55cda0e6c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644882"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Řízení infrastruktury Azure DevTest Labs – migrace a integrace aplikací
Jakmile je vaše vývojové /testovací prostředí zřízeno, musíte se zamyslet nad následujícími otázkami:

- Jak využíváte prostředí v rámci vašeho projektového týmu?
- Jak zajistíte, že budete dodržovat požadované zásady organizace a zachovat flexibilitu přidat hodnotu vaší aplikace?

## <a name="azure-marketplace-images-vs-custom-images"></a>Image Azure Marketplace vs. vlastní image

### <a name="question"></a>Otázka
Kdy mám použít image Azure Marketplace vs. vlastní image organizace?

### <a name="answer"></a>Odpověď
Azure Marketplace by se měl používat ve výchozím nastavení, pokud nemáte konkrétní obavy nebo organizační požadavky. Některé běžné příklady zahrnují;

- Komplexní nastavení softwaru, které vyžaduje, aby aplikace byla zahrnuta jako součást základní bitové kopie.
- Instalace a nastavení aplikace může trvat mnoho hodin, což není efektivní využití výpočetního času, které mají být přidány na image Azure Marketplace.
- Vývojáři a testeři vyžadují rychlý přístup k virtuálnímu počítači a chtějí minimalizovat dobu nastavení nového virtuálního počítače.
- Dodržování předpisů nebo regulační podmínky (například zásady zabezpečení), které musí být zavedeny pro všechny počítače.

Použití vlastních obrázků by nemělo být považováno za lehce. Zavádějí další složitost, protože nyní musíte spravovat soubory VHD pro tyto základní image. Je také nutné pravidelně opravovat tyto základní bitové kopie aktualizacemi softwaru. Tyto aktualizace zahrnují nové aktualizace operačního systému (OS) a všechny aktualizace nebo změny konfigurace potřebné pro samotný softwarový balíček.

## <a name="formula-vs-custom-image"></a>Vzorec vs. vlastní obrázek

### <a name="question"></a>Otázka
Kdy mám použít vzorec vs. vlastní obrázek?

### <a name="answer"></a>Odpověď
Rozhodujícím faktorem v tomto scénáři je obvykle náklady a opětovné použití.

Pokud máte scénář, kdy mnoho uživatelů nebo testovacích prostředí vyžaduje bitovou kopii s velkým množstvím softwaru nad základní bitovou kopii, můžete snížit náklady vytvořením vlastní bitové kopie. To znamená, že obraz je vytvořen jednou. Snižuje dobu nastavení virtuálního počítače a náklady vzniklé v důsledku spuštění virtuálního počítače při instalaci.

Dalším faktorem, který je třeba poznamenat, je však četnost změn softwarového balíčku. Pokud spustíte denní sestavení a budete vyžadovat, aby tento software byl na virtuálních počítačích vašich uživatelů, zvažte použití vzorce namísto vlastní bitové kopie.

## <a name="use-custom-organizational-images"></a>Použití vlastních organizačních bitových kopií

### <a name="question"></a>Otázka
Jak lze nastavit snadno opakovatelný proces, který přenese mé vlastní organizační obrázky do prostředí DevTest Labs?

### <a name="answer"></a>Odpověď
Podívejte se [na toto video na vzoru Image Factory](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Tento scénář je pokročilý scénář a poskytované skripty jsou pouze ukázkové skripty. Pokud jsou požadovány nějaké změny, je třeba spravovat a udržovat skripty používané ve vašem prostředí.

Použití devTest Labs k vytvoření vlastního kanálu image v Azure Pipelines:

- [Úvod: Příprava virtuálních počítačen během několika minut nastavením továrny na image v Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Image Factory - Část 2! Nastavení azure potrubí a tovární laboratoře k vytváření virtuálních počítačů](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Image Factory - Část 3: Uložit vlastní obrázky a distribuovat do více labs](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Video: Vlastní továrna na image s Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Vzory pro nastavení konfigurace sítě

### <a name="question"></a>Otázka
Jak zajistím, aby se vývojové a testovací virtuální počítače nemohly dostat k veřejnému internetu? Existují nějaké doporučené vzory pro nastavení konfigurace sítě?

### <a name="answer"></a>Odpověď
Ano. Existují dva aspekty, které je třeba zvážit – příchozí a odchozí provoz.

**Příchozí provoz** – Pokud virtuální počítač nemá veřejnou IP adresu, nemůže být dosaženo prostřednictvím internetu. Běžným přístupem je zajistit, aby byla nastavena zásada na úrovni předplatného, takže žádný uživatel nebude moci vytvořit veřejnou IP adresu.

**Odchozí provoz** – Pokud chcete zabránit tomu, aby virtuální počítače směřovaly přímo k veřejnému internetu a vynutit provoz prostřednictvím podnikové brány firewall, můžete směrovat provoz místně přes expresní trasu nebo VPN pomocí vynuceného směrování.

> [!NOTE]
> Pokud máte proxy server, který blokuje provoz bez nastavení proxy serveru, nezapomeňte přidat výjimky do účtu úložiště artefaktů testovacího prostředí .

Skupiny zabezpečení sítě můžete také použít pro virtuální počítače nebo podsítě. Tento krok přidá další vrstvu ochrany povolit / blokovat provoz.

## <a name="new-vs-existing-virtual-network"></a>Nová vs. stávající virtuální síť

### <a name="question"></a>Otázka
Kdy mám vytvořit novou virtuální síť pro prostředí DevTest Labs vs. pomocí existující virtuální sítě?

### <a name="answer"></a>Odpověď
Pokud vaše virtuální počítače potřebují interakci s existující infrastrukturou, měli byste zvážit použití existující virtuální sítě v prostředí DevTest Labs. Kromě toho pokud používáte ExpressRoute, můžete minimalizovat množství virtuálních sítí / podsítí tak, aby nedošlo k fragmentaci ip adresního prostoru, který se přiřadí k použití v předplatných. Měli byste také zvážit použití vzoru partnerského vztahu virtuální sítě zde (model Hub-Spoke). Tento přístup umožňuje komunikaci virtuálnísítě/podsítě mezi předplatnými v rámci dané oblasti, i když partnerský vztah napříč oblastmi je nadcházející funkcí v azure sítích.

V opačném případě může mít každé prostředí DevTest Labs vlastní virtuální síť. Všimněte si však, že existují [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md) počtu virtuálních sítí na předplatné. Výchozí částka je 50, i když tento limit lze zvýšit na 100.

## <a name="shared-public-or-private-ip"></a>Sdílená, veřejná nebo soukromá IP adresa

### <a name="question"></a>Otázka
Kdy mám použít sdílenou IP adresu vs. veřejnou IP adresu vs. soukromou IP adresu?

### <a name="answer"></a>Odpověď
Pokud používáte síť VPN nebo Express Route mezi webovými servery, zvažte použití privátních IP služeb tak, aby vaše počítače byly přístupné prostřednictvím interní sítě a nepřístupné prostřednictvím veřejného internetu.

> [!NOTE]
> Vlastníci testovacího prostředí můžete změnit tuto zásadu podsítě zajistit, že nikdo omylem vytvoří veřejné IP adresy pro své virtuální chod. Vlastník předplatného by měl vytvořit zásady předplatného, které zabrání vytváření veřejných IP služeb.

Při použití sdílených veřejných IP adres virtuální počítače v testovacím prostředí sdílejí veřejnou IP adresu. Tento přístup může být užitečný, když potřebujete zabránit porušení limitů na veřejné IP adresy pro dané předplatné.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Omezení počtu virtuálních počítačů na uživatele nebo testovací prostředí

### <a name="question"></a>Otázka
Existuje pravidlo, pokud jde o to, kolik virtuálních počítačů mám nastavit na uživatele nebo na testovací prostředí?

### <a name="answer"></a>Odpověď
Při zvažování počtu virtuálních počítačů na uživatele nebo na testovací prostředí existují tři hlavní problémy:

- **Celkové náklady,** které může tým utratit za prostředky v testovacím prostředí. Je snadné roztočit mnoho strojů. Pro řízení nákladů je jedním mechanismem omezení počtu virtuálních uživatelů na uživatele a/nebo na testovací prostředí.
- Celkový počet virtuálních počítačů v testovacím prostředí je ovlivněn [kvótami na úrovni předplatného,](../azure-resource-manager/management/azure-subscription-service-limits.md) které jsou k dispozici. Jedním z horních limitů je 800 skupin prostředků na předplatné. DevTest Labs aktuálně vytvoří novou skupinu prostředků pro každý virtuální virtuální ms (pokud se nepoužívají sdílené veřejné IP adresy). Pokud existuje 10 testovacích prostředí v předplatném, testovací prostředí by se vešly přibližně 79 virtuálních počítačů v každé laboratoři (800 horní limit – 10 skupin prostředků pro 10 testovacích prostředí sami) = 79 virtuálních počítačů na testovací prostředí.
- Pokud je testovací prostředí připojené k místnímu prostřednictvím expresní trasy (například), jsou pro virtuální síť/podsíť **k dispozici definované adresní prostory IP.** Chcete-li zajistit, že virtuální chody v testovacím prostředí nepodaří vytvořit (chyba: nelze získat IP adresu), vlastníci testovacího prostředí můžete zadat maximální virtuální chodů na testovací prostředí zarovnány s IP adresní prostor k dispozici.

## <a name="use-resource-manager-templates"></a>Použití šablon Resource Manageru

### <a name="question"></a>Otázka
Jak lze používat šablony Správce prostředků v prostředí DevTest Labs?

### <a name="answer"></a>Odpověď
Nasadíte šablony Správce prostředků do prostředí DevTest Labs pomocí kroků uvedených v funkci Prostředí v článku [DevTest labs.](devtest-lab-test-env.md) V podstatě zkontrolujete šablony Správce prostředků do úložiště Git (azure úložiště nebo GitHub) a přidáte [privátní úložiště pro šablony](devtest-lab-test-env.md) do testovacího prostředí.

Tento scénář nemusí být užitečné, pokud používáte DevTest Labs k hostování vývojových počítačů, ale může být užitečné, pokud vytváříte pracovní prostředí, které je reprezentativní pro produkční prostředí.

Je také třeba poznamenat, že počet virtuálních počítačů na testovací prostředí nebo na uživatele možnost omezuje pouze počet počítačů nativně vytvořených v samotném testovacím prostředí, a nikoli v žádném prostředí (šablony Správce prostředků).

## <a name="next-steps"></a>Další kroky
Viz [Použití prostředí v devTest Labs](devtest-lab-test-env.md).