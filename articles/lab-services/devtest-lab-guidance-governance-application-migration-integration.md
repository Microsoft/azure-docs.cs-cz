---
title: Zásady správného řízení infrastruktury Azure DevTest Labs
description: Tento článek obsahuje pokyny pro zásady správného řízení infrastruktury Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: c57385bc398544e786aec77dae9886784e3a0b3c
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243616"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Zásady správného řízení infrastruktury Azure DevTest Labs - migrace aplikací a integrace
Po vytvoření prostředí laboratoře vývoj/testování, je potřeba uvažovat o na následující otázky:

- Jak využít prostředí v rámci týmu projektu?
- Jak je zajistit, že řídí všechny požadované zásady organizace a udržovat flexibilitu, abyste mohli přidat hodnotu do vaší aplikace?

## <a name="azure-marketplace-images-vs-custom-images"></a>Image z Azure Marketplace vs. vlastní Image

### <a name="question"></a>Otázka
Kdy použít image Azure Marketplace vs. vlastní organizační vlastní image?

### <a name="answer"></a>Odpověď
Pokud nemáte konkrétní otázky nebo požadavků organizace by měla sloužit ve výchozím nastavení Azure Marketplace. Mezi běžné příklady patří;

- Nastavení složitý software, který vyžaduje, aby aplikace byly zahrnuty základní image.
- Instalace a nastavení aplikace může trvat mnoho hodin, které nejsou efektivní využití výpočetního času má být přidán do Image Azure Marketplace.
- Vývojářům a testerům rychle vyžadují přístup k virtuálnímu počítači a chcete minimalizovat čas instalace nového virtuálního počítače.
- Dodržování předpisů nebo právní podmínky (třeba zásady zabezpečení), které musí být splněné všechny počítače.

Použití vlastní Image by neměly být zahrnuté lehce. Přinášejí další složitost, protože teď muset spravovat soubory virtuálního pevného disku pro tyto podpůrné základní Image. Také je potřeba pravidelně oprava tyto základní Image s aktualizacemi softwaru. Tyto aktualizace zahrnují nové aktualizace operačního systému (OS) a všechny aktualizace nebo změny konfigurace, které jsou potřebné pro balíček softwaru, samotného.

## <a name="formula-vs-custom-image"></a>Vzorec vs. vlastní image

### <a name="question"></a>Otázka
Kdy použít vzorec vs. vlastní image?

### <a name="answer"></a>Odpověď
Obvykle rozhodujícím faktorem v tomto scénáři je nákladů a znovu použít.

Pokud máte scénáře, kde mnoho uživatelů/testovacích prostředí vyžadují image s velkým množstvím softwaru nad základní image, může snížit náklady tím, že vytvoříte vlastní image. To znamená, že po vytvoření image. Snižuje čas nastavení virtuálního počítače a náklady, protože virtuální počítač spuštěn, když dojde k nastavení.

Dodatečný faktor si je ale frekvence změny vašeho softwarového balíčku. Pokud spustíte denní sestavení a vyžadovat, aby software na virtuálních počítačích uživatelů, zvažte použití vzorec namísto vlastní image.

## <a name="use-custom-organizational-images"></a>Použít vlastní Image organizace

### <a name="question"></a>Otázka
Jak nastavím snadno opakovatelné procesu uvést Moje vlastní organizační Image v prostředí DevTest Labs?

### <a name="answer"></a>Odpověď
Zobrazit [toto video na objekt pro vytváření bitové kopie vzoru](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Tento scénář je pokročilý scénář a skripty k dispozici jsou pouze ukázkové skripty. Pokud jsou vyžadovány žádné změny, musíte pro správu a údržbu skripty používané ve vašem prostředí.

Použití k vytvoření vlastní image kanálu v kanálech Azure DevTest Labs:

- [Představení: Příprava virtuálních počítačů během několika minut díky nastavení objektu pro vytváření bitové kopie ve službě Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Obrázek Factory – 2. část! Nastavit kanály pro Azure a objekt pro vytváření testovacího prostředí k vytvoření virtuálních počítačů](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Obrázek Factory – část 3: Uložení vlastních Imagí a distribuovat do více testovacích prostředí](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Video: Objekt pro vytváření vlastní Image s Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Vzorce, a nastavení konfigurace sítě

### <a name="question"></a>Otázka
Jak zajistit, že vývojové a testovací virtuální počítače se nám kontaktovat veřejného Internetu? Existují všechny doporučené způsoby nastavení konfigurace sítě?

### <a name="answer"></a>Odpověď
Ano. Existují dva aspekty ke zvážení – příchozí a odchozí provoz.

**Příchozí provoz** – Pokud virtuální počítač nemá veřejnou IP adresu, nelze získat přístup k Internetu. Běžným přístupem je zajistit, že je nastavena zásady na úrovni předplatného, tak, že žádný uživatel je možné vytvořit veřejnou IP adresu.

**Odchozí provoz** – Pokud chcete zabránit virtuálních počítačů přejdete přímo do veřejného Internetu a vynucení provozu prostřednictvím podnikové brány firewall, pak může směrovat provoz místních přes expressroute nebo VPN, pomocí vynucené směrování.

> [!NOTE]
> Pokud máte proxy server, která blokuje provoz bez nastavení proxy serveru, nezapomeňte do účtu úložiště artefaktů v testovacím prostředí, přidejte výjimky.

Můžete také použít skupiny zabezpečení sítě pro virtuální počítače nebo podsítí. Tento krok přidává další vrstvu ochrany pro povolení / blokování provozu.

## <a name="new-vs-existing-virtual-network"></a>Nové nebo existující virtuální sítě

### <a name="question"></a>Otázka
Kdy je vhodné vytvořit novou virtuální síť pro Moje prostředí DevTest Labs oproti použití existující virtuální sítě?

### <a name="answer"></a>Odpověď
Pokud vaše virtuální počítače potřebují komunikovat s stávající infrastruktury, pak měli byste zvážit použití existující virtuální sítě v prostředí DevTest Labs. Kromě toho, pokud používáte ExpressRoute, můžete chtít minimalizovat množství virtuálních sítí / podsítí tak, aby váš adresní prostor IP adres, který získá přiřazen pro použití v předplatných nejsou fragment. Byste také zvážit použití virtuální síť vytvoření partnerského vztahu vzoru tady (centra s paprsky modelu). Tento přístup umožňuje komunikaci virtuálních sítí/podsítí napříč předplatnými v rámci dané oblasti, i když partnerský vztah napříč oblastmi je funkce odesílaných nahoru v sítích Azure.

Každé prostředí DevTest Labs, jinak může mít svůj vlastní virtuální sítě. Mějte však na paměti, že existují [omezení](../azure-subscription-service-limits.md) na počet virtuálních sítí na jedno předplatné. Výchozí doba je 50, i když tento limit lze zvýšit na 100.

## <a name="shared-public-or-private-ip"></a>Sdílené, veřejné nebo privátní IP adresu

### <a name="question"></a>Otázka
Kdy použít se sdílené IP Adresou a veřejnou IP adresu a privátní IP adresa?

### <a name="answer"></a>Odpověď
Pokud používáte síť VPN site-to-site nebo Expressroute, zvažte použití privátních IP adres tak, že vaše počítače jsou přístupné prostřednictvím vaší interní síti a nejsou přístupné prostřednictvím veřejného Internetu.

> [!NOTE]
> Vlastníků testovacího prostředí můžete změnit tyto zásady podsítě k zajištění, že nikdo omylem vytvoří veřejné IP adresy pro své virtuální počítače. Vlastník předplatného byste vytvořit zásady předplatného brání vytváření veřejné IP adresy.

Při použití sdílené veřejné IP adresy, virtuální počítače v testovacím prostředí, sdílet veřejnou IP adresu. Tento přístup může být užitečné, když budete chtít zabránit porušení omezení pro veřejné IP adresy pro daný odběr.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Omezení počtu virtuálních počítačů na uživatele nebo testovacího prostředí

### <a name="question"></a>Otázka
Existuje pravidlo z hlediska počtu virtuálních počítačů, že by měla nastavit na uživatele nebo počet v testovacím prostředí?

### <a name="answer"></a>Odpověď
Při zvažování počet virtuálních počítačů na uživatele nebo počet v testovacím prostředí, existují tři aspekty:

- **Celkové náklady** , které tým můžete utratit za prostředky v testovacím prostředí. Je snadné zprovoznění velký počet počítačů. Chcete-li mít náklady pod kontrolou, jeden mechanismus je omezit počet virtuálních počítačů na uživatele a/nebo počet v testovacím prostředí
- Celkový počet virtuálních počítačů v testovacím prostředí má vliv [úrovni předplatného kvóty](../azure-subscription-service-limits.md) k dispozici. Jeden z horních mezí je 800 skupin prostředků na předplatné. DevTest Labs aktuálně vytvoří novou skupinu prostředků pro každý virtuální počítač (Pokud se používají sdílené veřejné IP adresy). Pokud v předplatném 10 labs, labs může přizpůsobit přibližně 79 virtuální počítače v každé testovací prostředí (800 horní limit – 10 skupiny prostředků pro 10 labs sami) = 79 virtuálních počítačů podle testovacího prostředí.
- Pokud testovací prostředí je připojený k místnímu přes Express Route (například), jsou **definované adresní prostory IP adres k dispozici** pro virtuální síť/podsíť. K zajištění, že virtuální počítače v testovacím prostředí není nepodaří vytvořit (Chyba: Nelze získat IP adresu), vlastníků testovacího prostředí můžete určit maximální počet virtuálních počítačů podle testovacího prostředí v souladu s adresní prostor IP adres k dispozici.

## <a name="use-resource-manager-templates"></a>Použití šablon Resource Manageru

### <a name="question"></a>Otázka
Jak lze pomocí šablon Resource Manageru v mého prostředí DevTest Labs?

### <a name="answer"></a>Odpověď
Nasadit své šablony Resource Manageru prostředí v DevTest Labs pomocí kroků uvedených v [funkce prostředí v DevTest labs](devtest-lab-test-env.md) článku. V podstatě, zkontrolujte své šablony Resource Manageru do úložiště Git (úložiště Azure nebo GitHub) a přidejte [soukromé úložiště k šablonám](devtest-lab-test-env.md) testovací prostředí.

Tento scénář nemusí být užitečné, pokud používáte DevTest Labs hostovat vývojové počítače, ale může být užitečné, pokud vytváříte pracovní prostředí, které je zástupce produkčního prostředí.

Je také vhodné poznamenat, že počet virtuálních počítačů podle testovacího prostředí nebo možnost uživatelů pouze omezí počet počítačů, které jsou vytvořené nativně v samotné testovací prostředí a ne všechny prostředí (šablon Resource Manageru).

## <a name="next-steps"></a>Další postup
Zobrazit [používat prostředí v DevTest Labs](devtest-lab-test-env.md).