---
title: Migrace a integrace aplikací v Azure DevTest Labs
description: Tento článek poskytuje pokyny pro řízení infrastruktury Azure DevTest Labs v kontextu migrace a integrace aplikací.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8d86728eefc46c74b49ac610e2207ce5e7ae6a9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289345"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Řízení Azure DevTest Labs infrastruktury – migrace a integrace aplikací
Po navázání vývojového a testovacího prostředí je potřeba vzít v úvahu následující otázky:

- Jak využít prostředí v rámci vašeho projektového týmu?
- Jak zajistíte, že budete dodržovat požadované zásady organizace a zachováte flexibilitu pro přidání hodnoty do vaší aplikace?

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Marketplace obrázků vs. vlastní image

### <a name="question"></a>Otázka
Kdy mám použít Azure Marketplace image vs. vlastní organizační image?

### <a name="answer"></a>Odpověď
Azure Marketplace by se měla používat ve výchozím nastavení, pokud nemáte konkrétní obavy nebo organizační požadavky. Mezi běžné příklady patří:

- Komplexní instalace softwaru, která vyžaduje, aby aplikace byla součástí základní image.
- Instalace a nastavení aplikace může trvat mnoho hodin, což není efektivní využití výpočetního času k přidání na Azure Marketplace image.
- Vývojáři a testeri vyžadují rychlý přístup k virtuálnímu počítači a chtějí minimalizovat dobu nastavení nového virtuálního počítače.
- Podmínky dodržování předpisů nebo regulativní podmínky (například zásady zabezpečení), které musí být na místě pro všechny počítače.

Použití vlastních imagí by se nemělo považovat za lehce. Představují velmi složitost, protože teď musíte spravovat soubory VHD pro tyto základní image. Také je potřeba rutinním pomocí aktualizací softwaru rutiny opravit. Tyto aktualizace zahrnují nové aktualizace operačního systému (OS) a všechny aktualizace nebo změny konfigurace, které jsou potřeba pro samotný softwarový balíček.

## <a name="formula-vs-custom-image"></a>Vzorec vs. vlastní image

### <a name="question"></a>Otázka
Kdy mám použít vzorec vs. vlastní image?

### <a name="answer"></a>Odpověď
Obvykle je rozhodující faktor v tomto scénáři náklady a znovu použít.

Pokud máte scénář, ve kterém mnoho uživatelů/cvičení vyžaduje image s velkým množstvím softwaru nad základní imagí, pak můžete snížit náklady vytvořením vlastní image. To znamená, že se obrázek vytvoří jednou. Zkracuje dobu nastavení virtuálního počítače a náklady vzniklé v důsledku toho, že dojde k instalaci na virtuálním počítači.

Dodatečný faktor k poznámení je ale frekvence změn balíčku softwaru. Pokud spouštíte každodenní sestavení a požadujete, aby byl software na virtuálních počítačích vašich uživatelů, zvažte použití vzorce místo vlastní image.

## <a name="use-custom-organizational-images"></a>Použití vlastních organizačních imagí

### <a name="question"></a>Otázka
Jak můžu snadno opakovat a přenést vlastní organizační image do prostředí DevTest Labs?

### <a name="answer"></a>Odpověď
Podívejte se [na toto video ve vzoru pro vytváření imagí](./devtest-lab-faq.md#blog-post). Tento scénář je pokročilý scénář a poskytují se jenom ukázkové skripty. Pokud se vyžadují nějaké změny, budete muset spravovat a udržovat skripty používané ve vašem prostředí.

Použití DevTest Labs k vytvoření vlastního kanálu obrázků v Azure Pipelines:

- [Úvod: Příprava virtuálních počítačů během několika minut nastavením objektu pro vytváření imagí v Azure DevTest Labs](./devtest-lab-faq.md#blog-post)
- [Továrna imagí – část 2! Nastavení Azure Pipelines a výrobního prostředí pro vytváření virtuálních počítačů](./devtest-lab-faq.md#blog-post)
- [Továrna obrázků – část 3: uložení vlastních imagí a distribuce do více cvičení](./devtest-lab-faq.md#blog-post)
- [Video: vytváření vlastních imagí pomocí Azure DevTest Labs](./devtest-lab-faq.md#blog-post)

## <a name="patterns-to-set-up-network-configuration"></a>Vzory pro nastavení konfigurace sítě

### <a name="question"></a>Otázka
Návody zajistěte, aby vývojové a testovací virtuální počítače nedokázaly získat přístup k veřejnému Internetu? Existují nějaké Doporučené vzory pro nastavení konfigurace sítě?

### <a name="answer"></a>Odpověď
Ano. K dispozici jsou dvě aspekty, které byste měli zvážit – příchozí a odchozí provoz.

**Příchozí provoz** – Pokud virtuální počítač nemá veřejnou IP adresu, nemusíte ho kontaktovat internetem. Běžným přístupem je zajistit, že jsou nastavené zásady na úrovni předplatného, takže žádný uživatel nemůže vytvořit veřejnou IP adresu.

**Odchozí provoz** – Pokud chcete zabránit virtuálním počítačům přejít přímo k veřejnému Internetu a vynutit provoz přes podnikovou bránu firewall, můžete směrovat provoz místně přes Express Route nebo VPN pomocí vynuceného směrování.

> [!NOTE]
> Pokud máte proxy server, která blokuje provoz bez nastavení proxy serveru, nezapomeňte přidat výjimky do účtu úložiště artefaktu testovacího prostředí.

Skupiny zabezpečení sítě můžete použít také pro virtuální počítače nebo podsítě. Tento krok přidá další vrstvu ochrany pro povolení/blokování provozu.

## <a name="new-vs-existing-virtual-network"></a>Nová a stávající virtuální síť

### <a name="question"></a>Otázka
Kdy je vhodné vytvořit novou virtuální síť pro prostředí DevTest Labs a použít stávající virtuální síť?

### <a name="answer"></a>Odpověď
Pokud vaše virtuální počítače potřebují komunikovat se stávající infrastrukturou, měli byste zvážit použití existující virtuální sítě v prostředí DevTest Labs. Pokud navíc používáte ExpressRoute, možná budete chtít minimalizovat množství virtuální sítě/podsítí, abyste nefragmentoval adresní prostor IP adres, který se přiřadí k použití v předplatných. Měli byste taky zvážit použití vzoru partnerského vztahu virtuálních sítí (hvězdicové model). Tento přístup umožňuje komunikaci VNet/Subnet napříč předplatnými v dané oblasti, i když partnerský vztah mezi oblastmi je funkce, která je součástí sítě Azure.

V opačném případě by každé prostředí DevTest Labs mohlo mít vlastní virtuální síť. Upozorňujeme však, že existují [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md) počtu virtuálních sítí na jedno předplatné. Výchozí hodnota je 50, i když tento limit lze vystoupl na 100.

## <a name="shared-public-or-private-ip"></a>Sdílená, veřejná nebo privátní IP adresa

### <a name="question"></a>Otázka
Kdy mám použít sdílenou IP adresu a veřejnou IP adresu a soukromou IP adresu?

### <a name="answer"></a>Odpověď
Pokud používáte síť Site-to-Site VPN nebo Express Route, zvažte použití privátních IP adres, aby vaše počítače byly přístupné prostřednictvím interní sítě a nepřístupné přes veřejný Internet.

> [!NOTE]
> Vlastníci testovacího prostředí můžou tyto zásady podsítě změnit, aby se zajistilo, že nikdo nechtěně nevytvoří veřejné IP adresy pro své virtuální počítače Vlastník předplatného by měl vytvořit zásady předplatného, které brání v vytváření veřejných IP adres.

Při použití sdílených veřejných IP adres sdílí virtuální počítače v testovacím prostředí veřejnou IP adresu. Tento přístup může být užitečný v případě, že se potřebujete vyhnout porušení limitů u veřejných IP adres daného předplatného.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Omezení počtu virtuálních počítačů na uživatele nebo testovací prostředí

### <a name="question"></a>Otázka
Je nějaké pravidlo, které se dá nastavit na počet virtuálních počítačů, které mám nastavit na uživatele nebo na testovací prostředí?

### <a name="answer"></a>Odpověď
Při zvažování počtu virtuálních počítačů na uživatele nebo na laboratorní prostředí existují tři hlavní aspekty:

- **Celkové náklady** , které tým může strávit na prostředky v testovacím prostředí. Mnoho počítačů je snadné. Pro řízení nákladů jeden mechanismus omezí počet virtuálních počítačů na uživatele nebo na jedno testovací prostředí.
- Celkový počet virtuálních počítačů v testovacím prostředí je ovlivněný dostupnými [kvótami na úrovni předplatného](../azure-resource-manager/management/azure-subscription-service-limits.md) . Jedna z horních limitů je 800 skupin prostředků v rámci předplatného. DevTest Labs aktuálně pro každý virtuální počítač vytvoří novou skupinu prostředků (Pokud se nepoužívají sdílené veřejné IP adresy). Pokud je v předplatném 10 laboratoří, laboratoře můžou do každého testovacího prostředí umístit přibližně 79 virtuálních počítačů (800 horní limit – 10 skupin prostředků pro sebe sama o 10 laboratoří) = 79 virtuálních počítačů na testovací prostředí.
- Pokud je testovací prostředí připojené k místní síti přes Express Route (například), jsou **k dispozici definované adresní prostory IP adres** pro virtuální síť nebo podsíť. Aby se zajistilo, že se virtuální počítače v testovacím prostředí nedaří vytvořit (Chyba: nejde získat IP adresu), vlastníci testovacího prostředí můžou určit maximální počet virtuálních počítačů na testovací prostředí, které jsou zarovnané na adresní prostor IP adres.

## <a name="use-resource-manager-templates"></a>Použití šablon Resource Manageru

### <a name="question"></a>Otázka
Jak můžu použít šablony Správce prostředků v prostředí DevTest Labs?

### <a name="answer"></a>Odpověď
Šablony Správce prostředků můžete nasadit do prostředí DevTest Labs pomocí kroků uvedených v tématu [funkce prostředí v článku DevTest Labs](devtest-lab-test-env.md) . V podstatě zkontrolujete šablony Správce prostředků do úložiště Git (buď Azure Repos nebo GitHub), a přidejte do testovacího prostředí [privátní úložiště pro vaše šablony](devtest-lab-test-env.md) .

Tento scénář nemusí být užitečný, pokud používáte DevTest Labs k hostování vývojových počítačů, ale může být užitečný, když vytváříte přípravné prostředí, které je reprezentativní pro produkci.

Také je potřeba poznamenat, že počet virtuálních počítačů na testovací prostředí nebo na uživatele omezuje jenom počet počítačů nativně vytvořených v testovacím prostředí, a ne podle prostředí (Správce prostředků šablony).

## <a name="next-steps"></a>Další kroky
Viz [použití prostředí v DevTest Labs](devtest-lab-test-env.md).
