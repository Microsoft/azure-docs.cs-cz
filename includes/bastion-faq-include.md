---
title: zahrnout soubor
description: zahrnout soubor
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e29a9265e010c3f442b742faf62b16dae02739fa
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191179"
---
### <a name="preview"></a>Jak účast ve verzi public preview

Potřebujete připojit k účasti ve verzi public preview. Postupujte podle kroků v [v tomto článku](../articles/bastion/bastion-create-host-portal.md) k vytvoření nového prostředku Azure Bastionu. V současné době při přístupu a používání této služby, je nutné použít [webu Azure portal – ve verzi preview](https://aka.ms/BastionHost) místo regulární webu Azure portal.

### <a name="regions"></a>Které oblasti jsou k dispozici ve verzi preview?

Můžete nasadit a používat Bastionu prostředku v některé z těchto oblastí přes ve verzi preview [webu Azure portal – ve verzi preview odkaz](https://aka.ms/BastionHost).

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="portal"></a>Nemůžu najít Bastionu prostředků na webu Azure Portal. Co bych měl/a dělat?

Ujistěte se, že používáte [webu Azure portal – ve verzi preview odkaz](https://aka.ms/BastionHost), ne regulární Azure portal.

### <a name="publicip"></a>Budu potřebovat veřejnou IP adresu na mém virtuálním počítači?

Veřejná IP adresa na virtuální počítače Azure, ke kterému se připojujete ke službě Azure Bastionu nepotřebujete. Služba Bastionu se otevře RDP/SSH relace nebo připojení k virtuálnímu počítači přes privátní IP adresa vašeho virtuálního počítače ve vaší virtuální síti.

### <a name="rdpssh"></a>Je třeba RDP nebo SSH klienta?

K využití připojení RDP/SSH k virtuálnímu počítači Azure na webu Azure Portal nepotřebujete klienta SSH ani RDP. Použití [webu Azure portal – ve verzi preview odkaz](https://aka.ms/BastionHost) pro přístup k cestě ve verzi preview portálu. Umožní vám získat přístup RDP/SSH k virtuálnímu počítači přímo z prohlížeče.

### <a name="agent"></a>Potřebuji agenta spuštěného ve virtuálním počítači Azure?

Do virtuálních počítačů Azure a prohlížeče nemusíte instalovat žádné agenty ani jiný software. Služba Bastion je bez agentů a pro RDP/SSH nevyžaduje žádný další software.

### <a name="browsers"></a>Jaké prohlížeče jsou podporovány?

Ve verzi public preview použijte prohlížeč Microsoft Edge nebo Google Chrome na Windows. Na počítačích Apple Mac používejte prohlížeč Google Chrome. V systému Windows i Mac se také podporuje Microsoft Edge Chromium.

### <a name="roles"></a>Jsou všechny role vyžadovaných pro přístup k virtuálnímu počítači?

Aby bylo možné navázat připojení, následující role jsou povinné:

* Role čtenáře na virtuálním počítači
* Role čtenáře na síťovou kartu s privátní IP adresa virtuálního počítače
* Role Čtenář u prostředku Azure Bastionu

### <a name="previewbill"></a>Ceny – se mi bude účtovat účast ve verzi preview?

Vám bude účtováno pouze částečně ve verzi public preview. Neexistuje ale žádná smlouva SLA připojené v rámci vašeho nasazení. Další informace najdete na [stránce s cenami](https://aka.ms/BastionHostPricing).