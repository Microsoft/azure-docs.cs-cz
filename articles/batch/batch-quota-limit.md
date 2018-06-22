---
title: Služba kvóty a omezení pro Azure Batch | Microsoft Docs
description: Další informace o výchozích Azure Batch kvót, omezení a omezení a zvyšuje jak požádat o kvótu
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12880ba3aa918873343ee8eb98e92130106e8362
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304448"
---
# <a name="batch-service-quotas-and-limits"></a>Kvóty a omezení služby Batch

Jako s jinými službami Azure neexistují omezení na některé prostředky spojené s touto službou Batch. Mnoho z těchto omezení je výchozí kvóty používané službou Azure na úrovni účet nebo předplatné. Tento článek popisuje tyto výchozí hodnoty a jak můžete vyžádat kvóty zvyšuje.

Mějte na těchto kvót podle návrhu a rozšiřování škálování využívajících vaší úloh služby Batch. Například pokud váš fond nemá dostat cílovým počtem výpočetních uzlů, které zadáte, může bylo dosaženo maximální kvóta základní vašeho účtu Batch.

Můžete spustit několik dávkových úloh služby Batch v jednom účtu Batch najednou, nebo můžete úlohy rozložit mezi více účtů Batch, které jsou v jednom předplatném, ale v různých oblastech Azure.

Pokud plánujete spouštět úlohy v produkčním prostředí ve službě Batch, musíte zvýšit jeden nebo více kvót nad výchozí. Pokud chcete kvótu zvýšit, můžete otevřít online [zákazníka žádost o podporu](#increase-a-quota) zdarma.

> [!NOTE]
> Kvótu je limit platební, není záruku kapacity. Pokud máte požadavků na kapacitu ve velkém měřítku, obraťte se na podporu Azure.
> 
> 

## <a name="resource-quotas"></a>Kvóty prostředků
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>Kvóty jader v uživatelském režimu předplatného

Pokud jste vytvořili účet Batch se režim přidělení fondu nastavený na **uživatele předplatné**, kvóty se použijí jinak. V tomto režimu, Batch virtuální počítače a další prostředky jsou vytvořené přímo v vaše předplatné při vytváření fondu. Kvóty jader Azure Batch se nevztahují na účet vytvořené v tomto režimu. Místo toho kvóty pro vaše předplatné pro místní výpočetní jader a dalším prostředkům se použijí. Další informace o těchto kvót v [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md).

## <a name="other-limits"></a>Další omezení
| **Prostředek** | **Maximální omezení** |
| --- | --- |
| [Souběžné úlohy](batch-parallel-node-tasks.md) na výpočetním uzlu |4 x počet jader na uzel |
| [Aplikace](batch-application-packages.md) na účtu Batch |20 |
| Balíčky aplikací na jednu aplikaci |40 |
| Úloha maximální doba platnosti | 7 dní<sup>1</sup> |
| Výpočetní uzly ve fondu komunikaci mezi uzly povolena | 100 |

<sup>1</sup> maximální životnost úlohy, když je přidán do úlohy po dokončení, je 7 dní. Dokončené úkoly se zachovávají po neomezenou dobu; data pro úkoly, které se nedokončí v rámci maximální doby života, nejsou dostupná.


## <a name="view-batch-quotas"></a>Zobrazení dávky kvóty
Zobrazení vaší kvóty účtu Batch [portál Azure][portal].

1. Vyberte **účty Batch** na portálu, pak vyberte účet Batch, co vás zajímá.
2. Vyberte **kvóty** v nabídce účtu Batch.
3. Zobrazit kvóty aktuálně používané k účtu Batch
   
    ![Kvóty účtu batch][account_quotas]



## <a name="increase-a-quota"></a>Navýšení kvóty
Postupujte podle těchto kroků k vyžádání kvótu zvýšit vašeho účtu Batch nebo předplatnému pomocí [portál Azure][portal]. Typ zvýšení kvóty závisí na režimu přidělení fondu vašeho účtu Batch.

### <a name="increase-a-batch-cores-quota"></a>Navýšení kvóty jader Batch 

1. Vyberte **Nápověda a podpora** dlaždici řídicího panelu portálu nebo otazník (**?**) v pravém horním rohu portálu.
2. Vyberte **nová žádost o podporu** > **Základy**.
3. V **Základy**:
   
    a. **Vydávání typu** > **kvóty**
   
    b. Vyberte své předplatné.
   
    c. **Typ kvóty** > **Batch**
   
    d. **Podporují plán** > **kvóty podporu - zahrnuté**
   
    Klikněte na **Další**.
4. V **problém**:
   
    a. Vyberte **závažnost** podle vaší [dopad na chod firmy][support_sev].
   
    b. V **podrobnosti**, zadejte každé kvóty, které chcete změnit, název účtu Batch a nový limit.
   
    Klikněte na **Další**.
5. V **kontaktní informace**:
   
    a. Vyberte **způsob kontaktu preferované**.
   
    b. Zkontrolujte a zadejte požadované kontaktní údaje.
   
    Kliknutím na **Vytvořit** odešlete žádost o podporu.

Jakmile jste odeslali vaši žádost o podporu, bude vás kontaktovat podporu Azure. Všimněte si, že dokončení požadavku může trvat až 2 pracovních dnů.


## <a name="related-topics"></a>Související témata
* [Vytvoření účtu Azure Batch pomocí portálu Azure](batch-account-create-portal.md)
* [Přehled funkcí Azure Batch](batch-api-basics.md)
* [Limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
