---
title: Testování vaší nabídky virtuálních počítačů pro Marketplace | Dokumentace Microsoftu
description: Pochopit, jak otestovat vaši image virtuálního počítače na webu Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: b90353dbbc5d019897735cfc05caa3ee094dfedc
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078809"
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Testování vaší nabídky virtuálních počítačů pro Azure Marketplace v testovacím prostředí
Příprava znamená nasazení skladové jednotky do privátního "izolovaném prostoru" kde můžete otestovat a ověřit funkčnost před jeho nasazení na webu Marketplace. SKU se zobrazí v pracovním stejně, jako by tomu bylo pro zákazníky, kteří ho nasadil. Předtím, než ji převede do přípravy, musí potvrdit vaší image virtuálního počítače.

## <a name="step-1-push-your-offer-to-staging"></a>Krok 1: Převedení vaší nabídky do přípravného prostředí
1. Na **publikovat** klikněte na tlačítko **přejdete k přípravám**.
   
    ![Kreslení](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. Pokud portálu pro publikování vás upozorní na jakékoli chyby, opravte je.
3. V **kdo má přístup k dvoufázové instalace nabídky?** dialogového okna zadejte seznam předplatných Azure, které použijete k náhledu na vaši nabídku v [webu Azure portal](https://portal.azure.com).
   
   > [!NOTE]
   > V případě virtuálních počítačů a šablon řešení prosím **nejsou** předplatné seznamu povolených IP adres na základě typu CSP, DreamSpark nebo Azure v programu Open.
   > 
   > 
   >
   > V případě virtuálních počítačů, když kliknete na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**, následující kroky se provádějí za scénu. Bude moct zobrazit průběh každého kroku na kartě publikovat publikování portálu. Je třeba zkontrolovat tuto stránku v pravidelných intervalech (dokud je ve stavu PŘIPRAVENÝ) pro všechny informace o selhání, které potřebují opravu z-endu vašeho.

   > - Zpočátku pracovní požadavek přejde na certifikaci týmu, který ověřit virtuální pevný disk. Ale pokud požadavek má obor pouze marketingové změnu, pak certifikace přeskočen.
   > - Po dokončení certifikace replikace z nabídky start v Azure datových centrech. Obvykle trvá 24-48hours na dokončení replikace, ale může trvat až týden v závislosti na velikosti virtuálního pevného disku. Pokud požadavek má obor pouze marketingové změnu, pak replikace je ale rychlejší.
   > - Po dokončení replikace poté nabídky bude k dispozici v [webu Azure portal](http:/portal.azure.com). V této době stav budou umístěné v publikování portálu. Fázované nabídka je viditelná v [webu Azure portal](http:/portal.azure.com) pouze pomocí e-mailu ID spojený s předplatným, které je vynášené nabídky.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí jedné z předplatných Azure uvedených v předchozím kroku.
2. Najděte nabídky a ověření vašich bodů image virtuálního počítače:
   
   * Ujistěte se, že marketingové obsah se zobrazí správně v tržišti.
   * Konec konec nasazení image virtuálního počítače.
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> Vaše nabídka zůstane v přípravě, dokud neoznámíte Microsoftu prostřednictvím portálu pro publikování [**publikovat** kartu > klikněte na tlačítko **"Žádost o schválení pro nabízených oznámení do produkční"**], že jste připravení přejít na produkčního prostředí. Je ideální chvíle, aby všichni členové vašeho týmu kontroly všechno v rámci přípravy na zveřejnění uvedené nabídky.
> 
> Testovací platforma je určená pro testování nabídky v režimu náhledu vydavatelem. Důrazně bránit použití této nepostradatelné pro obchodní účely.
> 
> 

## <a name="next-steps"></a>Další postup
Teď, když vaše nabídka je "připravené" a testování její funkčnosti a marketing content, můžete přejít do konečné fáze publikování **kroku 4**: [Nasazení vaší nabídky na webu Marketplace](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: Publikování nabídky na webu Azure Marketplace](marketplace-publishing-getting-started.md)

