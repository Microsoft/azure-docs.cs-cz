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
ms.openlocfilehash: 29ab9b3c33aae6005510c34b207c7f87714149e5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608125"
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

Vám bude účtováno pouze částečně ve verzi public preview. Neexistuje ale žádná smlouva SLA připojené k vašemu nasazení. Další informace najdete na [stránce s cenami](https://aka.ms/BastionHostPricing).

### <a name="previewbill"></a>Proč se zobrazí chyba "vypršela platnost vaší relace" zprávy před spuštěním relace Bastionu?

Relace měl iniciovat jenom z webu Azure portal. Přihlaste se k webu Azure portal a začít vaši relaci znovu. Pokud přejdete na adresu URL přímo z jiného relaci prohlížeče nebo karty, je tato chyba očekávaná. Pomáhá zajistit, že vaše relace je bezpečnější a že relace je přístupný pouze prostřednictvím webu Azure portal.
