---
title: Správa vaší bitové kopie virtuálního počítače v Azure Marketplace | Microsoft Docs
description: Podrobné informace týkající se po počáteční publikace spravovat vaše bitovou kopii virtuálního počítače v Azure Marketplace
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: mbaldwin;
ms.openlocfilehash: 19f60de4a6c651885da7ff1fb7612a77f3d0c8f1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "29949017"
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Po Provozní příručka pro virtuální počítač nabídky v Azure Marketplace
Tento článek vysvětluje, jak můžete aktualizovat nabídka migraci virtuálního počítače v Azure Marketplace. Provede vás provede procesem přidání jeden nebo více nových SKU pro existující nabídku. Také provede vás provede procesem odebrání nabídka migraci virtuálního počítače nebo SKU z Marketplace.

Po nabídka nebo SKU je dvoufázové instalace v [portál Azure](http://portal.azure.com), nelze změnit následující textová pole:

* **Nabízejí identifikátor**: V publikování portálu, přejděte na **virtuální počítače** a vyberte vaši nabídku. Pak klikněte na tlačítko **Image virtuálních počítačů** > **nabízejí identifikátor**.
* **Identifikátor SKU**: V publikování portálu, přejděte na **virtuální počítače** a vyberte vaši nabídku. Pak klikněte na tlačítko **SKU** > **přidat SKU**.
* **Vydavatel Namespace**: V publikování portálu, přejděte na **virtuální počítače** > **návod** > **Řekněte nám o vaše společnost**(tuto možnost najdete v části "Krok 2 zaregistrovat vaše společnost") > **vydavatele Namespace** > **Namespace**.

Po nabídka skladová položka je uvedena ve [Marketplace](http://azure.microsoft.com/marketplace), nelze změnit následující textová pole:

* **Nabízejí identifikátor**: V publikování portálu, přejděte na **virtuální počítače** a vyberte vaši nabídku. Pak klikněte na tlačítko **Image virtuálních počítačů** > **nabízejí identifikátor**.
* **Identifikátor SKU**: V publikování portálu, přejděte na **virtuální počítače** a vyberte vaši nabídku. Pak klikněte na tlačítko **SKU** > **přidat SKU**.
* **Vydavatel Namespace**: V publikování portálu, přejděte na **virtuální počítače** > **návod** > **Řekněte nám o vaše společnost**(tuto možnost najdete v části "Krok 2 Register") **vydavatele Namespace** > **Namespace**.
* **Porty**: V publikování portálu, přejděte na **virtuální počítače** a vyberte vaši nabídku. Pak klikněte na tlačítko **Image virtuálních počítačů** > **otevřete porty**.
* **Změna seznamu SKU(s) – ceny**
* **Změna modelu fakturace uvedené SKU(s)**
* **Odebrání fakturace oblasti uvedené SKU(s)**
* **Změna počet datových disků z uvedených SKU(s)**

## <a name="update-the-technical-details-of-a-sku"></a>Aktualizovat technické podrobnosti SKU
Přidat novou verzi na uvedené verze SKU a znovu publikovat vaši nabídku, postupujte takto:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **Image virtuálních počítačů** kartě.
4. V **SKU** část, vyhledejte SKU, který chcete aktualizovat.
5. Přidat nové číslo verze pro verze SKU a klikněte na **+** tlačítko. Nové verze musí být ve formátu X.Y.Z, kde X, Y a jsou celá čísla. Verze změny by měly být jenom přírůstkové.
6. V **URL virtuálního pevného disku operačního systému** zadejte sdílený přístupový podpis URI vytvořený pro operační systém virtuálního pevného disku a uložte změny.

   > [!IMPORTANT]
   > Můžete nelze přírůstek nebo snížení počet datových disků z uvedených SKU. Budete muset vytvořit nové SKU v tomto případě. Podrobné pokyny naleznete v části [přidat nové skladová položka v seznamu nabídky](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Přejděte na **publikovat** a klikněte na **PUSH pracovní**. Podrobné pokyny na vaši nabídku testování v testovacím prostředí najdete v tématu [otestovat vaši nabídku virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po otestování vaši nabídku při přípravě přejít na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

    ![Image virtuálních počítačů](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Aktualizovat podrobnosti běžné uživatele nabídku nebo SKU
Můžete aktualizovat běžné uživatele (marketingu, právní, podporu, kategorie) podrobností za provozu nabídka nebo skladová položka v Marketplace.

### <a name="update-the-offer-description-and-logos"></a>Aktualizovat popis nabídky a loga
Aktualizovat podrobnosti nabídky a znovu publikovat vaši nabídku, postupujte takto:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **MARKETING** kartě.
4. Klikněte na tlačítko **Angličtina (USA)**.
5. Klikněte **podrobnosti** kartě. V **popis** část, aktualizujte nabídku **název**, **Souhrn**, a **DLOUHÝ Souhrn** a uložte změny.

   > [!NOTE]
   > Při aktualizaci podrobnosti SKU, mějte na paměti tato omezení: 
   * Nezadávejte duplicitní text pro popis nabídky a popis SKU.
   * Nezadávejte text duplicitní název SKU a nabídku dlouhé shrnutí. 
   * Nezadávejte text duplicitní název SKU a souhrn nabídka.
   >
   >

    ![Podrobnosti](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. V **loga** části **podrobnosti** aktualizujte loga. Ujistěte se, že postupujte podle loga [pokyny pro Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > Ikona nejdůležitější je volitelný. Můžete tak, aby nahrajte ikonu nejdůležitější. Po odeslání ikonou nejdůležitější je však není možné odstranit z publikování portálu. Postupujte podle [nejdůležitější ikonu pokyny](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Přejděte na **publikovat** a klikněte na **PUSH pracovní**. Podrobné pokyny na vaši nabídku testování v testovacím prostředí najdete v tématu [otestovat vaši nabídku virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po otestování vaši nabídku při přípravě přejít na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

    ![Loga](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Popis SKU aktualizace
Aktualizovat podrobnosti SKU a znovu publikovat vaši nabídku, postupujte takto:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **MARKETING** kartě.
4. Klikněte na tlačítko **Angličtina (USA)**.
5. Klikněte **plány** kartě. V **SKU** část, aktualizace verze SKU **název**, **Souhrn**, a **popis** a uložte změny.

   > [!NOTE]
   > Při aktualizaci podrobnosti SKU, mějte na paměti tato omezení: 
   * Nezadávejte duplicitní text pro popis nabídky a popis SKU. 
   * Nezadávejte text duplicitní název SKU a nabídku dlouhé shrnutí. 
   * Nezadávejte text duplicitní název SKU a souhrn nabídka.
   >
   >
6. Přejděte na **publikovat** a klikněte na **PUSH pracovní**. Podrobné pokyny na vaši nabídku testování v testovacím prostředí najdete v tématu [otestovat vaši nabídku virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po otestování vaši nabídku při přípravě přejít na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

    ![Plány](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Změnit existující odkazy nebo přidat nové odkazy
Změnit existující odkazy nebo přidat nové odkazy a poté jej znovu publikovat vaši nabídku, postupujte takto:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **MARKETING** kartě.
4. Klikněte na tlačítko **Angličtina (USA)**.
5. Klikněte **odkazy** kartě.
6. Chcete-li přidat nový odkaz v **odkazy** klikněte na tlačítko **+ přidat odkaz**. V **přidat odkaz** dialogovém okně zadejte odkaz **název** a **URL** a uložte změny. Můžete zadat všechny odkaz, který obsahuje informace, které můžou pomoct zákazníků.
7. Aktualizovat nebo odstranit existující odkaz, vyberte odkaz a klikněte **upravit** tlačítko nebo **odstranit** tlačítko.

   > [!NOTE]
   > Ujistěte se, odkazy, které jste zadali v této části fungují správně, protože tyto odkazy získat ověření během zpracování žádosti o vaše produkční.
   >
   >
8. Přejděte na **publikovat** a klikněte na **PUSH pracovní**. Podrobné pokyny na vaši nabídku testování v testovacím prostředí najdete v tématu [otestovat vaši nabídku virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Po otestování vaši nabídku při přípravě přejít na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

    ![Odkazy](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Přidání odkazu](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Změňte stávající image ukázka nebo přidat novou bitovou kopii ukázka
Chcete-li změnit stávající image ukázka nebo přidat nové ukázkové obrázky a poté jej znovu publikovat vaši nabídku, postupujte takto:

> [!NOTE]
> Ukázkový pouze jeden obrázek se zobrazí v [portál Azure](https://portal.azure.com).
>
>

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **MARKETING** kartě.
4. Klikněte na tlačítko **Angličtina (USA)**.
5. Klikněte **UKÁZKOVÉ obrázky** kartě.
6. Chcete-li přidat novou bitovou kopii ukázkové, v **ukázkové obrázky** klikněte na tlačítko **NAHRÁT novou IMAGE A** a uložte změny.

   > [!NOTE]
   > Včetně ukázkového obrázku je volitelný.
   >
7. Přejděte na **publikovat** a klikněte na **PUSH pracovní**. Podrobné pokyny na vaši nabídku testování v testovacím prostředí najdete v tématu [otestovat vaši nabídku virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po otestování vaši nabídku při přípravě přejít na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

    ![Ukázkové obrázky](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>Aktualizace právní obsahu
Aktualizovat právní obsah a znovu publikovat vaši nabídku, postupujte takto:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **MARKETING** kartě.
4. Klikněte na tlačítko **Angličtina (USA)**.
5. Klikněte **právní** kartě. V **právní** část, aktualizovat vaše zásady nebo podmínky použití. Zadejte nebo vložte zásady nebo podmínky v **podmínky použití** pole a uložte změny.
6. Omezení počtu znaků pro právní podmínky použití je 1 milion znaků.
7. Přejděte na **publikovat** a klikněte na **PUSH pracovní**. Podrobné pokyny na vaši nabídku testování v testovacím prostředí najdete v tématu [otestovat vaši nabídku virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po otestování vaši nabídku při přípravě přejít na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

    ![Právní informace](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Aktualizovat informace o podporu
Chcete-li aktualizovat informace o podporu a znovu publikovat vaši nabídku, postupujte podle těchto kroků:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **podporu** kartě.
4. V **Engineering obraťte se na** část, aktualizujte engineering kontaktní údaje. Tyto informace se používají pouze pro vnitřní komunikace mezi partnery a Microsoft.
5. V **zákaznickou podporu** část, aktualizujte kontaktní údaje podpory a **podporu URL**. Tyto informace se používají pouze pro vnitřní komunikace mezi partnery a Microsoft.

   > [!NOTE]
   > Pokud chcete zadat pouze e-mailová podpora, zadejte na fiktivní telefonní číslo v **zákaznickou podporu** části. V takovém případě se místo toho používá e-mailu, které jste zadali.
   >
   >
6. Přejděte na **publikovat** a klikněte na **PUSH pracovní**. Podrobné pokyny na vaši nabídku testování v testovacím prostředí najdete v tématu [otestovat vaši nabídku virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po otestování vaši nabídku při přípravě přejít na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

    ![Podpora](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>Aktualizace kategorie
Aktualizovat v části kategorie pro vaši nabídku a znovu publikovat vaši nabídku, postupujte takto:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **kategorie** kartě.
4. V **kategorie** část, aktualizujte kategorie pro vaši nabídku a uložte změny. Můžete vybrat až pěti kategorií pro galerii Azure Marketplace.
5. Přejděte na **publikovat** a klikněte na **PUSH pracovní**. Podrobné pokyny na vaši nabídku testování v testovacím prostředí najdete v tématu [otestovat vaši nabídku virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
6. Po otestování vaši nabídku při přípravě přejít na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

    ![Kategorie](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Přidat nové skladová položka v seznamu nabídky
Pokud chcete přidat nový SKU do vaší nabídky za provozu, postupujte takto:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **SKU** kartě. Pak klikněte na tlačítko **přidat SKU**. 
4. V dialogovém okně zadejte **identifikátor SKU** na malá písmena. Vyberte **přineste si vlastní licenci (BYOL) fakturační model** zaškrtávací políčko, pokud chcete publikovat nové SKU s fakturační model BYOL. Jinak zrušte zaškrtnutí políčka. Klikněte na tlačítko značky k vytvoření nové SKU. Pokud jste nezvolili fakturační model BYOL, se automaticky nastaví model fakturace na každou hodinu. Pokud chcete 30denní bezplatné zkušební verze pro hodinové fakturační model, vyberte **jeden měsíc** pro **je k dispozici bezplatná zkušební verze?** Jinak vyberte možnost **ne zkušební verze**. (**Je k dispozici bezplatná zkušební verze?**  se zobrazí jenom v případě, že jste nevybrali BYOL při vytváření nové verze SKU.)

   > [!IMPORTANT]
   > **Skrýt tento SKU z Marketplace, protože by měl koupili vždy prostřednictvím šablonu řešení** by měla být **Ano** *pouze* jste schválení pro publikování šablonu řešení. Jinak, tato možnost by měla být vždy **ne**.
   >
   >
4. V nabídce na levé straně klikněte **Image virtuálních počítačů** kartě a získat nové verze SKU, kterou jste vytvořili.
5. Nové verze SKU, naleznete v tématu [získat certifikační pro bitové kopie virtuálních počítačů](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) pokyny.
6. Přidání marketingové materiály pro nové SKU naleznete v tématu [poskytují Marketplace marketingové obsah](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Chcete-li přidat informace o cenách za nové verze SKU, přečtěte si téma [nastavit vaše ceny](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Přejděte na **publikovat** a klikněte na **PUSH pracovní**. Podrobné pokyny na vaši nabídku testování v testovacím prostředí najdete v tématu [otestovat vaši nabídku virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Po otestování vaši nabídku při přípravě přejít na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

    ![Skladové položky](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Přidat SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Změnit počet datových disků pro uvedené SKU
Můžete nelze přírůstek nebo snížení počet datových disků z uvedených SKU. Budete muset vytvořit nové SKU v tomto případě. Podrobné pokyny naleznete v části [přidat nové skladová položka v seznamu nabídky](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Odstranit uvedené nabídka z Marketplace
Různé aspekty muset postarat v případě požadavku na odebrání za provozu nabídky. Pokud chcete získat pokyny od týmu podpory k odebrání uvedené nabídka z Marketplace, postupujte takto:

1. Vyvolat lístek podpory na [vytvořit incident](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) stránky.

2. Vyberte **typ problému** jako **Správa nabízí**a vyberte **kategorie** jako **úprava nabídka nebo skladová položka již v produkčním prostředí**.
3. Odešlete žádost.

Tým podpory vás provede proces odstranění nabídky nebo SKU.

> [!NOTE]
> Když se stav Koncept (ale ne pracovním nebo produkčním), můžete odstranit vždy nabídku. Na **HISTORIE** , klikněte na **ZAHODIT koncept**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Odstranit uvedené SKU z Marketplace
Pokud chcete odstranit uvedené SKU z Marketplace, postupujte takto:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).

2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V podokně na levé straně klikněte **SKU** kartě.
4. Vyberte SKU, který chcete odstranit a klikněte na **odstranit** tlačítko.
5. Přejděte na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.
6. Po nabídka je publikovat na webu Marketplace, verze SKU je odstraněn z Marketplace a portálu Azure.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Odstranit aktuální verzi uvedené SKU z Marketplace
Pokud chcete odstranit aktuální verzi uvedené SKU z Marketplace, postupujte takto: 

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).

2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **Image virtuálních počítačů** kartě.
4. Vybrat SKU, jejichž aktuální verze, kterou chcete odstranit a klikněte na **odstranit** tlačítko.
5. Přejděte na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.
6. Po nabídku získá publikovat na webu Marketplace, aktuální verze uvedené SKU je odstraněn z Marketplace a portálu Azure. Verze SKU je pak vrácena zpět jeho předchozí verze.

## <a name="revert-the-listing-price-to-production-values"></a>Vrátit výpis cena na produkční hodnoty
Můžete obnovit výpis cena na produkční hodnoty, postupujte takto:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).
2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **cenová** kartě.
4. Vyberte oblast, jejichž ceny chcete obnovit.

    ![Ceny oblastí](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. Pro SKU s hodinové fakturační model resetovat ceny pro všechna jádra, jako jsou v produkčním prostředí pro vybrané oblasti. Pro SKU s fakturační model BYOL zpřístupnit verze SKU v oblasti tak, že zaškrtnete políčko pro SKU v **EXTERNALLY-LICENSED (BYOL) SKU dostupnosti** části.

    ![Cenových úrovních](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Klikněte na tlačítko **AUTOPRICE jiných trzích podle ceny ve Spojených státech**.

   > [!NOTE]
   > Popisek tlačítka se může lišit v závislosti na oblasti, kterou jste vybrali. Vzhledem k tomu, že jsme vybrali Spojených státech amerických, je tlačítko označeno **AUTOPRICE ostatní trhů na základě ON ceny IN Spojené státy**.
   >
   >

    ![Autoprice](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. Stránka 1 Průvodce Autoprice, zvolte základní trhu a klikněte na **šipku** tlačítko.

    ![Základní trhu](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. Na stránce 2, zvolte plány služeb a měřidla (jádra) a klikněte na **šipku** tlačítko.

    ![Služba plány a měřidla](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. Na stránce 3, klikněte na **přepnutí všechny** vyberte všechny oblasti. Nebo můžete ručně zaškrtněte políčka pro konkrétní oblasti. Klikněte **šipku** tlačítko.

    ![Zvolte trzích](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. Na stránce 4, zkontrolujte kurzů a klikněte na **Dokončit**. Průvodce obnoví ceny podle vybrané položky.

11. Na **cenová** , klikněte na **zobrazit souhrn a změny**.
    Pro **zobrazení verze**, vyberte **koncept**a pro **porovnání s**, vyberte **produkční**. Pokud se zobrazí žádné cenové rozdíly, cenách úspěšně vráceny zpět na produkční hodnoty.

    ![Zobrazení souhrnu a změny](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Přejděte na **publikovat** a klikněte na **PUSH pracovní**. Podrobné pokyny na vaši nabídku testování v testovacím prostředí najdete v tématu [otestovat vaši nabídku virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
13. Po otestování vaši nabídku při přípravě přejít na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

## <a name="revert-the-billing-model-to-production-values"></a>Vrátit model fakturace na produkční hodnoty
Chcete-li vrátit model fakturace na produkční hodnoty, postupujte takto:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).

2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **SKU** kartě.
4. Klikněte **upravit** tlačítko vrátit fakturační model. V okně, které se otevře, zaškrtněte nebo zrušte **fakturace a licencování provádí externě z Azure (neboli model použití vlastní licence)** zaškrtávací políčko.

    ![Upravit fakturace](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Postupujte podle kroků v "Vrátit výpis cena na produkční hodnoty" v tomto článku.
6. Přejděte na **publikovat** a klikněte na **PUSH pracovní**. Podrobné pokyny na vaši nabídku testování v testovacím prostředí najdete v tématu [otestovat vaši nabídku virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po otestování vaši nabídku při přípravě přejít na **publikovat** ve publikování portálu. Klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Obnovit nastavení viditelnosti uvedené SKU pro produkční hodnota
Můžete obnovit nastavení viditelnosti uvedené SKU pro hodnota produkce, postupujte takto:

1. Přihlaste se k [publikování portál](https://publish.windowsazure.com).

2. Přejděte na **virtuální počítače** a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **SKU** kartě.
4. Vyberte vaše SKU a obnovit nastavení viditelnosti SKU pro produkční hodnota.

    ![Viditelnost](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Jakmile jste hotovi s změny, klikněte na tlačítko **požádat o schválení pro PUSH na produkční** se publikovat nabídku v Marketplace.

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme GET: Publikování nabídky pro Azure Marketplace](marketplace-publishing-getting-started.md)
* [Pochopení výběr vytváření sestav](marketplace-publishing-report-payout.md)
* [Změnit incentive prodejce, u vašeho poskytovatele Cloud Solution Provider](marketplace-publishing-csp-incentive.md)
* [Řešení běžných potíží publikování na webu Marketplace](marketplace-publishing-support-common-issues.md)
* [Získat podporu jako vydavatel](marketplace-publishing-get-publisher-support.md)
* [Vytvoření image virtuálního počítače na místě](marketplace-publishing-vm-image-creation-on-premise.md)
* [Vytvoření virtuálního počítače se systémem Windows na portálu Azure preview](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
