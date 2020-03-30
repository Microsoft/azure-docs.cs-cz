---
title: Kvóty a limity služeb – Azure Batch | Dokumenty společnosti Microsoft
description: Informace o výchozích kvótách, limitech a omezeních Azure Batch a o tom, jak požádat o zvýšení kvóty
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c8a78acfa1f3e7332cee337ae1a82ee365fe356a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248226"
---
# <a name="batch-service-quotas-and-limits"></a>Kvóty a omezení služby Batch

Stejně jako u jiných služeb Azure existují omezení na určité prostředky spojené se službou Batch. Mnoho z těchto limitů jsou výchozí kvóty používané Azure na úrovni předplatného nebo účtu. Tento článek popisuje tyto výchozí hodnoty a jak můžete požádat o zvýšení kvóty.

Při navrhování a rozšiřování úloh batch mějte na paměti tyto kvóty. Pokud například váš fond nedosáhne cílového počtu výpočetních uzlů, které jste zadali, možná jste dosáhli základního limitu kvóty pro váš účet Batch.

Můžete spustit několik dávkových úloh služby Batch v jednom účtu Batch najednou, nebo můžete úlohy rozložit mezi více účtů Batch, které jsou v jednom předplatném, ale v různých oblastech Azure.

Pokud plánujete spustit produkční úlohy v batch, budete muset zvýšit jednu nebo více kvót nad výchozí. Chcete-li zvýšit kvótu, můžete zdarma otevřít [online žádost o zákaznickou podporu.](#increase-a-quota)

## <a name="resource-quotas"></a>Kvóty prostředků

Kvóta je úvěrový limit, nikoli záruka kapacity. Pokud máte rozsáhlé kapacity, obraťte se na podporu Azure.

Všimněte si také, že kvóty nejsou zaručené hodnoty. Kvóty se mohou lišit v závislosti na změnách ze služby Batch nebo požadavku uživatele na změnu hodnoty kvóty.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Kvóty jader v režimu předplatného uživatele

Pokud jste vytvořili dávkový účet s režimem přidělení fondu nastaveným na **odběr uživatele**, kvóty se použijí odlišně. V tomto režimu jsou dávkové virtuální počítače a další prostředky vytvořeny přímo ve vašem předplatném při vytvoření fondu. Kvóty jader Azure Batch se nevztahují na účet vytvořený v tomto režimu. Místo toho se použijí kvóty ve vašem předplatném pro místní výpočetní jádra a další prostředky. Další informace o těchto kvótách najdete v [limitech předplatného a služeb Azure, kvótách a omezeních](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Omezení velikosti fondu

Omezení velikosti fondu jsou nastavena službou Batch. Na rozdíl od [kvót prostředků](#resource-quotas)tyto hodnoty nelze změnit. Pouze fondy s meziuzpy komunikace a vlastní image mají omezení odlišné od standardní kvóty.

| **Zdrojů** | **Maximální omezení** |
| --- | --- |
| **Výpočetní uzly ve [fondu povolené komunikace mezi uzly](batch-mpi.md)**  ||
| Režim přidělení fondu dávkových služeb | 100 |
| Režim přidělení fondu dávkového předplatného | 80 |
| **Výpočetní uzly ve [fondu vytvořené pomocí prostředku spravované bitové kopie](batch-custom-images.md)**<sup>1</sup> ||
| Vyhrazené uzly | 2000 |
| Uzly s nízkou prioritou | 1000 |

<sup>1</sup> Pro fondy, které nejsou povoleny mezi uzlovací mise.

## <a name="other-limits"></a>Jiné limity

Další limity nastavené službou Batch. Na rozdíl od [kvót prostředků](#resource-quotas)tyto hodnoty nelze změnit.

| **Zdrojů** | **Maximální omezení** |
| --- | --- |
| [Souběžné úlohy](batch-parallel-node-tasks.md) na výpočetní uzel | 4 x počet jádra uzlů |
| [Aplikace](batch-application-packages.md) na dávkový účet | 20 |
| Balíčky aplikací pro aplikaci | 40 |
| Balíčky aplikací na fond | 10 |
| Maximální doba trvání úlohy | 180 dní<sup>1</sup> |
| [Připojení](virtual-file-mount.md) na výpočetní uzel | 10 |

<sup>1</sup> Maximální doba života úkolu od přidání do úlohy až po dokončení je 180 dní. Dokončené úkoly přetrvávají po dobu sedmi dnů; údaje o úkolech, které nebyly dokončeny během maximální životnosti, nejsou přístupná.

## <a name="view-batch-quotas"></a>Zobrazit dávkové kvóty

Zobrazení kvót účtu Batch na [webu Azure Portal][portal].

1. Na portálu vyberte **Dávkové účty** a vyberte dávkový účet, který vás zajímá.
1. V nabídce Dávkového účtu vyberte **Kvóty.**
1. Zobrazení kvót aktuálně použitých pro účet Batch

    ![Kvóty dávkového účtu][account_quotas]

## <a name="increase-a-quota"></a>Zvýšení kvóty

Chcete-li požádat o zvýšení kvóty pro svůj účet Batch nebo předplatné pomocí [portálu Azure,][portal]postupujte takto . Typ zvýšení kvóty závisí na režimu přidělení fondu účtu Batch. Chcete-li požádat o zvýšení kvóty, musíte zahrnout řadu virtuálních zařízení, pro kterou chcete kvótu zvýšit. Při použití zvýšení kvóty se použije na všechny řady virtuálních discích.

### <a name="increase-cores-quota-in-batch"></a>Zvýšení kvóty jader v batch 

1. Vyberte dlaždici **Nápověda + podpora** na řídicím panelu portálu nebo otazník (**?**) v pravém horním rohu portálu.
1. Vyberte možnost**Základy** **žádosti o** > novou podporu .
1. V **základech**:
   
    a. **Omezení typu** > **problému a předplatné (kvóty)**
   
    b. Vyberte své předplatné.
   
    c. **Dávka typu** > **kvóty**
      
    Vyberte **další**.
    
1. Oblast **Details** (Podrobnosti) obsahuje:
      
    a. V **poli Poskytnout podrobnosti**zadejte umístění, typ kvóty a účet Dávka.
    
    ![Zvýšení dávkové kvóty][quota_increase]

    Typy kvót zahrnují:

    * **Na dávkový účet**  
        Hodnoty specifické pro jeden účet Batch, včetně vyhrazených jader s nízkou prioritou a počtu úloh a fondů.
        
    * **Podle regionu**  
        Hodnoty, které platí pro všechny účty batch v oblasti a zahrnují počet účtů batch na oblast na předplatné.

    Kvóta s nízkou prioritou je jedna hodnota napříč všemi řadami virtuálních aplikací. Pokud potřebujete omezené jednotky skutuž, musíte vybrat **jady s nízkou prioritou** a zahrnout rodiny virtuálních zařízení, které chcete požádat.

    b. Vyberte **závažnost podle** dopadu vaší [firmy][support_sev].

    Vyberte **další**.

1. V **kontaktních informacích**:
   
    a. Vyberte **metodu upřednostňovaného kontaktu**.
   
    b. Ověřte a zadejte požadované kontaktní údaje.
   
    Chcete-li odeslat žádost o podporu, vyberte **možnost Vytvořit.**

Jakmile odeslate žádost o podporu, podpora Azure vás bude kontaktovat. Žádosti o kvóty mohou být dokončeny během několika minut nebo až dvou pracovních dnů.

## <a name="related-quotas-for-vm-pools"></a>Související kvóty pro fondy virtuálních společností

Dávkové fondy v konfiguraci virtuálního počítače nasazené ve virtuální síti Azure automaticky přidělují další síťové prostředky Azure. Pro každých 50 uzlů fondu ve virtuální síti jsou potřeba následující prostředky:

* Jedna [skupina zabezpečení sítě](../virtual-network/security-overview.md#network-security-groups)
* Jedna [veřejná IP adresa](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Jeden [odvykač zatížení](../load-balancer/load-balancer-overview.md)

Tyto prostředky jsou přiděleny v předplatném, které obsahuje virtuální síť dodaná při vytváření fondu dávek. Pro tyto prostředky platí omezení [kvót prostředků](../azure-resource-manager/management/azure-subscription-service-limits.md) předplatného. Pokud plánujete nasazení velkého fondu ve virtuální síti, zkontrolujte kvóty předplatného pro tyto prostředky. V případě potřeby požádejte o zvýšení na webu Azure Portal výběrem **možnosti Nápověda + podpora**.


## <a name="related-topics"></a>Související témata
* [Vytvoření účtu Azure Batch na webu Azure Portal](batch-account-create-portal.md)
* [Přehled funkcí Azure Batch](batch-api-basics.md)
* [Omezení předplatného a služeb Azure, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
