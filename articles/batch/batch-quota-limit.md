---
title: Kvóty a omezení služby – Azure Batch | Microsoft Docs
description: Přečtěte si o výchozích Azure Batch kvótách, omezeních a omezeních a o zvýšení kvóty.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 118a36587667c815489ddad03426b5cfcfee7382
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036687"
---
# <a name="batch-service-quotas-and-limits"></a>Kvóty a omezení služby Batch

Stejně jako u jiných služeb Azure platí omezení pro určité prostředky přidružené ke službě Batch. Mnohé z těchto omezení jsou výchozími kvótami, které Azure používá na úrovni předplatného nebo účtu. Tento článek pojednává o těchto výchozích nastaveních a o tom, jak se dá zvýšit kvóta.

Při návrhu a navýšení kapacity úloh služby Batch mějte na paměti tyto kvóty. Například pokud váš fond nedosahuje cílového počtu výpočetních uzlů, které jste zadali, možná jste dosáhli limitu základní kvóty pro účet Batch.

Můžete spustit několik dávkových úloh služby Batch v jednom účtu Batch najednou, nebo můžete úlohy rozložit mezi více účtů Batch, které jsou v jednom předplatném, ale v různých oblastech Azure.

Pokud máte v plánu provozovat produkční úlohy ve službě Batch, možná budete muset zvýšit jednu nebo více kvót nad výchozí hodnotou. Pokud chcete vygenerovat kvótu, můžete zdarma otevřít online [žádost o zákaznickou podporu](#increase-a-quota) .

## <a name="resource-quotas"></a>Kvóty prostředků

Kvóta je kreditního limitu záruku kapacity. Pokud máte velké nároky na kapacitu, obraťte se prosím na podporu Azure.

Všimněte si také, že kvóty nejsou Garantované hodnoty. Kvóty se můžou lišit v závislosti na změnách ze služby Batch nebo na žádost uživatele o změnu hodnoty kvóty.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Kvóty jader v režimu předplatného uživatele

Pokud jste vytvořili účet Batch s režimem přidělování fondů nastaveným na **předplatné uživatele**, kvóty se uplatní odlišně. V tomto režimu se virtuální počítače a další prostředky služby Batch vytvářejí přímo ve vašem předplatném při vytvoření fondu. Kvóty Azure Batchch jader se nevztahují na účet vytvořený v tomto režimu. Místo toho se použijí kvóty ve vašem předplatném pro regionální výpočetní jádra a další prostředky. Přečtěte si další informace o těchto kvótách v [omezeních, kvótách a omezeních předplatného a služeb Azure](../azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Omezení velikosti fondu

Omezení velikosti fondu nastavuje služba Batch. Na rozdíl od [kvót prostředků](#resource-quotas)nelze tyto hodnoty změnit. Omezení odlišná od standardní kvóty mají jenom fondy, které mají komunikaci mezi uzly a vlastní image.

| **Prostředek** | **Maximální omezení** |
| --- | --- |
| **Výpočetní uzly ve [fondu s povolenými komunikacemi mezi uzly](batch-mpi.md)**  ||
| Režim přidělování fondů služby Batch | 100 |
| Režim přidělování fondů předplatných dávky | 80 |
| **Výpočetní uzly ve [fondu vytvořené pomocí vlastní image virtuálního počítače](batch-custom-images.md)**  <sup>1</sup> ||
| Vyhrazené uzly | 2000 |
| Uzly s nízkou prioritou | 1000 |

<sup>1</sup> pro fondy, které nejsou povolené komunikace mezi uzly.

## <a name="other-limits"></a>Další omezení

Další omezení nastavená službou Batch. Na rozdíl od [kvót prostředků](#resource-quotas)nelze tyto hodnoty změnit.

| **Prostředek** | **Maximální omezení** |
| --- | --- |
| [Souběžné úkoly](batch-parallel-node-tasks.md) na výpočetní uzel | 4 x počet jader uzlů |
| Počet [aplikací](batch-application-packages.md) na účet Batch | 20 |
| Balíčky aplikací na jednu aplikaci | 40 |
| Balíčky aplikací na fond | 10 |
| Maximální doba života úlohy | 180 dnů<sup>1</sup> |
| Počet [připojení](virtual-file-mount.md) na výpočetní uzel | 10 |

<sup>1</sup> maximální doba života úkolu, od kdy se přidá do úlohy po jeho dokončení, je 180 dní. Dokončené úlohy trvají po dobu sedmi dnů; data pro úlohy, které nejsou dokončená v rámci maximální doby života, nejsou dostupná.

## <a name="view-batch-quotas"></a>Zobrazit kvóty dávky

Zobrazení kvót účtu Batch v [Azure Portal][portal].

1. Na portálu vyberte **účty Batch** a potom vyberte účet Batch, na který vás zajímáte.
1. V nabídce účtu Batch vyberte **kvóty** .
1. Zobrazit kvóty aktuálně použité pro účet Batch

    ![Kvóty účtu Batch][account_quotas]

## <a name="increase-a-quota"></a>Zvýšení kvóty

Pomocí těchto kroků můžete požádat o zvýšení kvóty pro účet Batch nebo předplatné pomocí [Azure Portal][portal]. Typ zvýšení kvóty závisí na režimu přidělování fondů účtu Batch. Chcete-li požádat o zvýšení kvóty, je nutné zahrnout řadu virtuálních počítačů, pro kterou chcete kvótu zvýšit. Při použití zvýšení kvóty se tato kvóta použije na všechny řady virtuálních počítačů.

### <a name="increase-cores-quota-in-batch"></a>Zvýšení kvóty jader v dávce 

1. Na řídicím panelu portálu vyberte dlaždici **Nápověda a podpora** nebo otazník ( **?** ) v pravém horním rohu portálu.
1. Vyberte **nové základy žádosti o** > podporu.
1. **Základní informace**:
   
    a.  > **Omezení služby typu problém a předplatné (kvóty)**
   
    b. Vyberte své předplatné.
   
    c. **Dávka typu kvóty** > 
      
    Vyberte **Další**.
    
1. V **podrobnosti**:
      
    a. V části **Zadejte podrobnosti**zadejte umístění, typ kvóty a účet Batch.
    
    ![Zvýšení kvóty dávky][quota_increase]

    Mezi typy kvót patří:

    * **Na účet Batch**  
        Hodnoty specifické pro jeden účet Batch, včetně základních a s nízkou prioritou a počtu úloh a fondů.
        
    * **Na oblast**  
        Hodnoty, které se vztahují na všechny účty Batch v oblasti a zahrnují počet účtů Batch na oblast a předplatné.

    Kvóta s nízkou prioritou je jednou hodnotou v rámci všech řad virtuálních počítačů. Pokud potřebujete omezené skladové jednotky, musíte vybrat **jádra s nízkou prioritou** a zahrnout do žádosti rodiny virtuálních počítačů.

    b. Vyberte **závažnost** podle svého [obchodního dopadu][support_sev].

    Vyberte **Další**.

1. V **kontaktní údaje**:
   
    a. Vyberte **upřednostňovanou metodu kontaktu**.
   
    b. Ověřte a zadejte požadované kontaktní údaje.
   
    Vyberte **vytvořit** a odešlete žádost o podporu.

Po odeslání žádosti o podporu vás bude kontaktovat podpora Azure. Žádosti o kvótu se můžou dokončit během několika minut nebo do dvou pracovních dnů.

## <a name="related-quotas-for-vm-pools"></a>Související kvóty pro fondy virtuálních počítačů

Fondy služby Batch v konfiguraci virtuálního počítače nasazené ve virtuální síti Azure automaticky přidělují další síťové prostředky Azure. Pro každý uzel fondu 50 ve virtuální síti jsou potřeba tyto prostředky:

* Jedna [Skupina zabezpečení sítě](../virtual-network/security-overview.md#network-security-groups)
* Jedna [Veřejná IP adresa](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Jeden [Nástroj pro vyrovnávání zatížení](../load-balancer/load-balancer-overview.md)

Tyto prostředky se přidělují v rámci předplatného, které obsahuje virtuální síť, která se zadala při vytváření fondu Batch. Pro tyto prostředky platí omezení [kvót prostředků](../azure-subscription-service-limits.md) předplatného. Pokud plánujete nasazení rozsáhlých fondů ve virtuální síti, Prohlédněte si kvóty předplatného pro tyto prostředky. V případě potřeby požádejte o zvýšení Azure Portal tím, že vyberete **help + podpora**.


## <a name="related-topics"></a>Související témata
* [Vytvoření účtu Azure Batch pomocí Azure Portal](batch-account-create-portal.md)
* [Přehled funkcí Azure Batch](batch-api-basics.md)
* [Limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
