---
title: Zřízení fondu Azure Batch z vlastní image | Dokumentace Microsoftu
description: Vytvořte fond z vlastní image ke zřízení výpočetních uzlů, které obsahují software a data, která potřebujete pro vaši aplikaci. Vlastní Image jsou efektivní způsob, jak nakonfigurovat výpočetních uzlů pro spouštění úloh služby Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 10/04/2018
ms.author: lahugh
ms.openlocfilehash: 0bc43b82a987ab065677bdbb56de73ef341c249d
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752122"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Použití vlastní image k vytvoření fondu virtuálních počítačů 

Při vytváření fondu Azure Batch pomocí konfigurace virtuálního počítače zadejte image virtuálního počítače, které poskytuje operační systém pro každý výpočetní uzel ve fondu. Podporované image Azure Marketplace nebo vlastní image (image virtuálního počítače po vytvoření a konfiguraci sami), můžete vytvořit fond virtuálních počítačů. Vlastní image musí být *spravované image* prostředků ve stejném předplatném Azure a oblasti jako účet Batch.

## <a name="why-use-a-custom-image"></a>Proč používat vlastní image?

Když zadáte vlastní image, budete mít kontrolu nad konfigurací operačního systému a typ operačního systému a datové disky, který se má použít. Vlastní image může obsahovat aplikace a referenční data, která budou k dispozici na všech uzlech fondu služby Batch, jakmile jsou zřízené.

Použití vlastní image šetří čas při přípravě váš fond výpočetních uzlů pro spouštění vašich úloh Batch. I když můžete použít image Azure Marketplace a instalovat software na každém výpočetním uzlu po zřízení, může být efektivnější použitím vlastní image.

Použití vlastní image nakonfigurovaný pro váš scénář poskytují několik výhod:

- **Nakonfigurujte operační systém (OS)**. Můžete přizpůsobit konfiguraci disku image operačního systému. 
- **Před instalací aplikace.** Předinstalační aplikace na disk s operačním systémem, což je méně náchylný než instalace aplikací po zřízení výpočetních uzlů pomocí spouštěcího úkolu a efektivnější.
- **Ušetřete čas restartování na virtuálních počítačích.** Instalace aplikace obvykle vyžaduje restartování virtuálního počítače, který je časově náročné. Před nainstalováním aplikace můžete ušetřit čas restartování. 
- **Zkopírujte velmi velké objemy dat jednou.** Zařazení statická data spravovanou vlastní image zkopírováním do spravované image datové disky. To jenom je potřeba provést jednou a díky tomu budou data k dispozici pro každý uzel ve fondu.
- **Výběr typů disků nevidí.** Máte taky možnost výběru z použití služby premium storage pro disk s operačním systémem a datového disku.
- **Zvětší fondy na velké velikosti.** Pokud používáte spravovanou vlastní image k vytvoření fondu, fond můžou růst bez nutnosti vytvářet kopie objektu blob bitové kopie virtuálních pevných disků. 


## <a name="prerequisites"></a>Požadavky

- **Prostředek spravované image**. Vytvoření fondu virtuálních počítačů pomocí vlastní image, musíte mít nebo vytvořit prostředek spravované image ve stejném předplatném Azure a oblasti jako účet Batch. Na obrázku musí být vytvořené ze snímků disku s operačním systémem Virtuálního počítače a volitelně jeho připojené datové disky. Další informace a kroky pro přípravu spravované image najdete v následující části. 
  - Použijte jedinečný vlastní image pro každý fond, který vytvoříte.
  - Vytvoření fondu s použitím image pomocí rozhraní API služby Batch, zadejte **ID prostředku** obrázku, který je ve formátu `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Na portálu, použijte **název** bitové kopie.  
  - Prostředek spravované image by měla existovat po dobu životnosti fondu umožňuje vertikálně navýšit kapacitu a je možné odebrat po odstranění fondu.

- **Ověřování Azure Active Directory (AAD)**. Rozhraní API klienta Batch musí používat ověřování AAD. Podpora Azure Batch pro AAD je popsána v [ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Příprava vlastní image

V Azure můžete připravit spravované image ze snímků operační systém virtuálním počítači Azure a datových disků, generalizovaného virtuálního počítače Azure se spravovanými disky nebo virtuální pevný disk zobecněný lokálně, který nahrajete. Škálování fondů služby Batch spolehlivě s použitím vlastní image, doporučujeme vytvoření spravované image pomocí *pouze* první metoda: pomocí funkce vytváření snímků disků Virtuálního počítače. Podívejte se na následující postup k přípravě virtuálního počítače, pořízení snímku a vytvořit image ze snímku. 

### <a name="prepare-a-vm"></a>Příprava virtuálního počítače 

Pokud vytváříte nový virtuální počítač pro bitovou kopii, a použít image Azure Marketplace podporované službou Batch jako základní image pro spravované image a pak ji přizpůsobit.  Pokud chcete získat seznam odkazů na obrázky Azure Marketplace podporuje služby Azure Batch, najdete v článku [SKU agenta uzlu seznamu](/rest/api/batchservice/account/listnodeagentskus) operace. 

> [!NOTE]
> Nelze použít bitovou kopii třetí strany, který má další licenci a podmínky nákupu jako základní image. Informace o těchto imagí Marketplace najdete v tématu pokyny pro [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) nebo [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) virtuálních počítačů.


* Ujistěte se, že virtuální počítač se vytvoří při použití spravovaného disku. Toto je výchozí nastavení úložiště při vytváření virtuálního počítače.
* Neinstalujte rozšíření Azure, jako je například rozšíření vlastních skriptů na virtuálním počítači. Pokud image obsahuje nějaké předinstalované rozšíření, Azure setkat s problémy při nasazení fondu služby Batch.
* Zajistěte, aby image základního operačního systému, který poskytnete používala výchozí dočasnou jednotku. Agent uzlu Batch aktuálně očekává, že výchozí dočasnou jednotku.
* Jakmile je virtuální počítač spuštěný, k němu připojte přes RDP (pro Windows) nebo SSH (pro Linux). Nainstalovat potřebný software nebo zkopírovat požadovaná data.  

### <a name="create-a-vm-snapshot"></a>Vytvoření snímku virtuálního počítače

Snímek je kopie virtuálního pevného disku úplné, jen pro čtení. Pokud chcete vytvořit snímek operačního systému Virtuálního počítače nebo datových disků, můžete na webu Azure portal nebo nástrojů příkazového řádku. Kroky a možnosti pro vytvoření snímku, najdete pokyny pro [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) nebo [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) virtuálních počítačů.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Vytvoření image z jedné nebo více snímků

K vytvoření spravované image ze snímku pomocí nástrojů příkazového řádku Azure, jako [az image vytvořit](/cli/azure/image) příkazu. Vytvoření image tak, že zadáte snímku disku operačním systémem a volitelně jeden nebo více snímků disku data.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Vytvořit fond z vlastní image na portálu

Jakmile jste uložili vlastní image a znáte jeho ID nebo název, vytvoření fondu služby Batch z této image. Následující kroky ukazují, jak vytvořit fond z portálu Azure portal.

> [!NOTE]
> Pokud vytváříte fond pomocí jednoho z rozhraní API služby Batch, ujistěte se, že identita, kterou používáte pro ověřování AAD má oprávnění pro prostředek obrázku. Zobrazit [ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md).
>

1. Na webu Azure Portal přejděte ke svému účtu Batch. Tento účet musí být ve stejném předplatném a oblasti jako skupinu prostředků obsahující vlastní image. 
2. V **nastavení** na levé straně vyberte okno **fondy** položky nabídky.
3. V **fondy** okna, vyberte **přidat** příkazu.
4. Na **přidat fond** okně **vlastní Image (Linux/Windows)** z **typ obrázku** rozevíracího seznamu. Z **vlastní image virtuálního počítače** rozevíracím seznamu vyberte název image (krátký tvar ID prostředku).
5. Vyberte správné **vydavatel/nabídka/Sku** pro vaši vlastní image.
6. Zadejte zbývající požadovaná nastavení, včetně **velikost uzlu**, **cílové vyhrazené uzly**, a **s nízkou prioritou uzly**, stejně jako všechny požadované volitelná nastavení.

    Například pro Microsoft Windows Server Datacenter 2016 vlastní image **přidat fond** okna se zobrazí, jak je znázorněno níže:

    ![Přidat fond z vlastní image Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Pokud chcete zkontrolovat, zda existujícího fondu vychází z vlastní image, najdete v článku **operačního systému** vlastnictví v souhrnu prostředků z **fondu** okno. V případě, že byl fond vytvořen z vlastní image, je nastavena na **vlastní Image virtuálního počítače**.

Všech vlastních imagí, které jsou přidružené k fondu se zobrazují na fondu **vlastnosti** okna.

## <a name="considerations-for-large-pools"></a>Důležité informace týkající se velké fondy

Pokud budete chtít vytvořit fond s využitím stovek virtuálních počítačů nebo další prvky pomocí vlastní image, je potřeba postupujte podle předchozích pokynů použít obraz vytvořené ze snímku virtuálního počítače.

Mějte také na paměti následující:

- **Omezení velikosti** – Batch omezuje velikost fondu na 2 500 vyhrazené výpočetní uzly a uzly s nízkou prioritou 1 000, při použití vlastní image.

  Pokud používáte stejnou bitovou kopii (nebo víc imagí založených na stejný základní snímek) a vytvořili více fondů, celkový počet výpočetních uzlů ve fondech nemůže být delší než předchozí omezení. Nedoporučujeme používat image nebo její podkladové snímku pro více než jeden fond.

  Pokud nakonfigurujete fond s může dojít k omezení limitů [příchozí fondy NAT](pool-endpoint-configuration.md).

- **Změnit velikost časového limitu** – Pokud jej váš fond obsahuje pevný počet uzlů (ne automatického škálování), zvyšte resizeTimeout vlastnost fondu, aby hodnota jako například 20 – 30 minut. Pokud váš fond nemá přístup do jeho velikost cíle v rámci časového limitu, provedete další [změnit velikost operace](/rest/api/batchservice/pool/resize).

  Pokud máte v plánu s více než 300 výpočetní uzly fondu, můžete potřebovat pro změnu velikosti fondu více než jednou k dosažení cílovou velikost.

## <a name="next-steps"></a>Další postup

- Podrobný přehled služby Batch, najdete v části [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md).
