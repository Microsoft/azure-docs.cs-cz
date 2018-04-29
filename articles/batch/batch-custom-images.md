---
title: Zřídit fondech Azure Batch z vlastních bitových kopií | Microsoft Docs
description: Můžete vytvořit dávce fondu vlastní image zřídit výpočetní uzly, které obsahují software a data, která je nutné pro vaši aplikaci. Vlastní Image jsou účinný způsob, jak nakonfigurovat výpočetních uzlů k spouštět úlohy Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 78bc50a1189d8f42281f81643a5e907d94480082
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="use-a-managed-custom-image-to-create-a-pool-of-virtual-machines"></a>Spravované vlastní image použít k vytvoření fondu virtuálních počítačů 

Při vytváření fondu Azure Batch pomocí konfigurace virtuálního počítače, můžete zadat image virtuálního počítače, které poskytuje operační systém pro každý výpočetní uzel ve fondu. Fond virtuálních počítačů můžete vytvořit image Azure Marketplace nebo vlastní image (image virtuálního počítače je vytvořen a nakonfigurován sami). Vlastní obrázek musí být *spravované image* prostředků ve stejném předplatném Azure a v oblasti jako účet Batch.

## <a name="why-use-a-custom-image"></a>Proč používat vlastní image?
Když zadáte vlastní image, budete mít kontrolu nad konfigurace operačního systému a typ operačního systému a datové disky, který se má použít. Vlastní image můžete zahrnout aplikace a referenční data, která je k dispozici na všech uzlech fondu Batch, při které byly povoleny.

Při přípravě váš fond výpočetních uzlů ke spuštění úlohy Batch pomocí vlastní image šetří čas. I když můžete použít image Azure Marketplace a instalovat software na každém výpočetním uzlu po zřízení, může být efektivnější pomocí vlastní image.

Použití vlastní image nakonfigurované pro váš scénář poskytují několik výhod:

- **Konfigurace operačního systému (OS)**. Zvláštní konfiguraci operačního systému můžete provádět na disk vlastní image operačního systému. 
- **Před instalací aplikace.** Můžete vytvořit vlastní image předinstalované aplikace na disku operačního systému, který je efektivnější a méně náchylný než instalace aplikace po zřízení pomocí StartTask výpočetní uzly.
- **Ušetřit čas restartování na virtuálních počítačích.** Instalace aplikace obvykle vyžaduje restartování virtuálního počítače, který je časově náročná. Před nainstalováním aplikace můžete ušetřit čas restartování. 
- **Zkopírujte velmi velké objemy dat jednou.** Statických dat součástí spravované vlastní image můžete nastavit zkopírováním na spravované image datových disků. To jenom je nutné provést jednou a zpřístupní data na všech uzlech fondu.
- **Výběr typů disku.** Spravované vlastní image můžete vytvořit z virtuálního pevného disku, na spravovaného disku virtuálního počítače Azure, snímek tyto disky nebo vlastní instalace Linux nebo Windows, které jste nakonfigurovali. Máte možnost použití služby premium storage pro disk operačního systému a datový disk.
- **Fondy dosáhnout jakékoli velikosti.** Spravované vlastní image, že použijete k vytvoření fondu, fond růst, aby jakékoli velikosti, které požadujete. Není nutné vytvořit kopie objektu blob bitové kopie virtuálních pevných disků, aby dokázala pojmout počet virtuálních počítačů. 


## <a name="prerequisites"></a>Požadavky

- **Prostředek spravované image**. Vytvoření fondu virtuálních počítačů pomocí vlastní image, musíte vytvořit prostředek spravované bitové kopie ve stejném předplatném Azure a v oblasti jako účet Batch. Možnosti přípravy bitové kopie spravované najdete v následující části.
- **Ověřování Azure Active Directory (AAD)**. Klient Batch API musí používat ověřování AAD. Podporu Azure Batch pro AAD je popsána v [řešení služby Batch ověření pomocí služby Active Directory](batch-aad-auth.md).

    
## <a name="prepare-a-custom-image"></a>Příprava vlastní image
Můžete připravit bitovou kopii spravované z disku VHD, virtuální počítač Azure s spravované disky nebo snímek virtuálního počítače. Pro dávku doporučujeme vytvoření spravovaného image z virtuálního počítače s spravované disky nebo snímek virtuálního počítače. Spravované bitové kopie a základní prostředků pro fondy škálování by měla existovat a může být odebrán po odstranění fondu. 

Příprava bitové kopie, mějte na paměti následující body:

* Zajistěte, aby image základního operačního systému, kterou použijete ke zřizování fondů Batch, neobsahovala žádná předinstalovaná rozšíření Azure, jako je například rozšíření vlastních skriptů. Pokud image obsahuje nějaké předinstalované rozšíření, v Azure může docházet k problémům při nasazování virtuálního počítače.
* Zajistěte, aby používala základní image operačního systému, které poskytnete výchozí dočasné jednotce. Agent uzlu Batch aktuálně očekává výchozí dočasné jednotce.
* Zdroj spravované image odkazuje fondu služby Batch nelze odstranit po dobu jeho existence fondu. Je-li zdroj spravované image odstraněna, pak fondu nemůže růst žádné další. 

### <a name="to-create-a-managed-image"></a>Chcete-li vytvořit bitovou kopii spravované
Všechny existující připravené systému Windows nebo Linux disk operačního systému můžete použít k vytvoření spravovaného bitové kopie. Například pokud chcete použít místní bitovou kopii, pak nahrajte místního disku do účtu Azure Storage, který je ve stejném předplatném, oblasti jako váš účet Batch pomocí AzCopy nebo jiné nástroje pro odesílání. Podrobný postup nahrání virtuálního pevného disku a vytvoření bitové kopie spravované, viz příručka pro [Windows](../virtual-machines/windows/upload-generalized-managed.md) nebo [Linux](../virtual-machines/linux/upload-vhd.md) virtuálních počítačů.

Můžete také připravit bitovou kopii spravované z nového nebo existujícího virtuálního počítače Azure nebo snímek virtuálního počítače. 

* Pokud vytváříte nový virtuální počítač, můžete image Azure Marketplace použít jako základní bitovou kopii pro spravované image a pak ji přizpůsobit. 

* Pokud plánujete zaznamenat bitovou kopii pomocí portálu se ujistěte, že je virtuální počítač vytvořený s spravovaným diskem. Toto je výchozí nastavení úložiště při vytváření virtuálního počítače.

* Jakmile je virtuální počítač spuštěný, lze k němu připojte pomocí protokolu RDP (pro Windows) nebo SSH (pro Linux). Nainstalujte potřebný software nebo zkopírujte požadované data a pak generalize virtuálního počítače.  

Postup generalize virtuální počítač Azure a vytvoření spravovaného bitové kopie, naleznete v části Pokyny pro [Windows](../virtual-machines/windows/capture-image-resource.md) nebo [Linux](../virtual-machines/linux/capture-image.md) virtuálních počítačů.

V závislosti na tom, jak budete chtít vytvoření fondu služby Batch Image musíte tento identifikátor bitové kopie:

* Pokud budete chtít vytvořit fond s bitovou kopii pomocí rozhraní API služby Batch, **ID prostředku** bitové kopie, což je ve formátu `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. 
* Pokud máte v plánu používat portál, **název** bitové kopie. 





## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Vytvořit fond z vlastní image na portálu

Jakmile jste uložili vlastní bitovou kopii a znáte jeho názvem nebo ID prostředků, můžete vytvořit fondu služby Batch z této bitové kopie. Následující kroky ukazují, jak vytvořit fond z portálu Azure.

> [!NOTE]
> Při vytváření fondu pomocí jedné z rozhraní API služby Batch, ujistěte se, že identita, který použijete pro ověřování AAD má oprávnění k prostředku bitové kopie. V tématu [řešení služby Batch ověření pomocí služby Active Directory](batch-aad-auth.md).
>

1. Na webu Azure Portal přejděte ke svému účtu Batch. Tento účet musí být ve stejném předplatném, oblasti, skupinu prostředků obsahující vlastní image. 
2. V **nastavení** okno na levé straně vyberte **fondy** položku nabídky.
3. V **fondy** vyberte **přidat** příkaz.
4. Na **přidat fond** vyberte **vlastní obrázek (Linux nebo Windows)** z **typ obrázku** rozevíracího seznamu. Z **image virtuálního počítače vlastní** rozevíracího seznamu, vyberte název bitové kopie (zkratka pro ID prostředku).
5. Vyberte správný **vydavatele nebo nabídka nebo Sku** pro vlastní bitovou kopii.
6. Zadejte zbývající požadované nastavení, včetně **velikost uzlu**, **cílové uzly vyhrazené**, a **nízkou prioritu uzly**, stejně jako všechny potřeby volitelná nastavení.

    Například pro Microsoft Windows Server Datacenter 2016 vlastní image **přidat fond** okno se zobrazí, jak je uvedeno níže:

    ![Přidejte fond z vlastní bitovou kopii systému Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Pokud chcete zkontrolovat, zda existující fond je založena na vlastní image, najdete v části **operačního systému** vlastnost v souhrnu oddílu prostředků **fondu** okno. Pokud fond byl vytvořen z vlastní image, je nastavený na **vlastní Image virtuálního počítače**.

Všechny vlastní Image, které jsou přidružené k fondu se zobrazí na fondu **vlastnosti** okno.
 
## <a name="next-steps"></a>Další postup

- Podrobnější přehled služby Batch, najdete v tématu [rozsáhlé paralelní vývoj výpočetní řešení pomocí služby Batch](batch-api-basics.md).
