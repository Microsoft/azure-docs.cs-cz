---
title: Správa vaší image virtuálního počítače na webu Azure Marketplace | Dokumentace Microsoftu
description: Podrobné pokyny o tom, jak spravovat po počáteční publikování image virtuálního počítače na webu Azure Marketplace
services: Azure Marketplace
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
ROBOTS: NOINDEX
ms.openlocfilehash: 7aea357c22a928d8af96791bf9cb4bd3c94f37b4
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077058"
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Průvodce postprodukční nabídky virtuálního počítače na webu Azure Marketplace
Tento článek vysvětluje, jak můžete aktualizovat nabídku na živé virtuálního počítače na webu Azure Marketplace. Provede vás provede procesem přidávání jeden nebo více nové skladové položky do existující nabídky. Také provede vás procesem jeho odebrání nabídky živé virtuálního počítače nebo skladové položky z webu Marketplace.

Po nabídka/SKU je umístěné v [webu Azure portal](http://portal.azure.com), nebudete moct změnit následující textová pole:

* **Nabízejí identifikátor**: V publikování portál, přejděte na **virtuálních počítačů** a vyberte možnost nabídky. Pak klikněte na tlačítko **IMAGÍ virtuálních počítačů** > **nabízejí identifikátor**.
* **Identifikátor SKU**: V publikování portál, přejděte na **virtuálních počítačů** a vyberte možnost nabídky. Pak klikněte na tlačítko **SKU** > **přidejte skladovou jednotku**.
* **Vydavatel Namespace**: V publikování portál, přejděte na **virtuálních počítačů** > **návod** > **Řekněte nám o vaši společnost** (nalezené pod "Register kroku 2 vaší Společnost") > **vydavatele Namespace** > **Namespace**.

Po nabídka/SKU je uveden v [Marketplace](https://azure.microsoft.com/marketplace), nebudete moct změnit následující textová pole:

* **Nabízejí identifikátor**: V publikování portál, přejděte na **virtuálních počítačů** a vyberte možnost nabídky. Pak klikněte na tlačítko **IMAGÍ virtuálních počítačů** > **nabízejí identifikátor**.
* **Identifikátor SKU**: V publikování portál, přejděte na **virtuálních počítačů** a vyberte možnost nabídky. Pak klikněte na tlačítko **SKU** > **přidejte skladovou jednotku**.
* **Vydavatel Namespace**: V publikování portál, přejděte na **virtuálních počítačů** > **návod** > **Řekněte nám o vaši společnost** (tuto možnost najdete v části "Krok 2 Register") **Vydavatele Namespace** > **Namespace**.
* **Porty**: V publikování portál, přejděte na **virtuálních počítačů** a vyberte možnost nabídky. Pak klikněte na tlačítko **IMAGÍ virtuálních počítačů** > **otevření portů**.
* **Změna skladové jednotky uvedené ceny**
* **Změna modelu fakturace uvedené skladové jednotky**
* **Odebrání fakturace oblastech uvedených skladové jednotky**
* **Změna počet datových disků uvedené skladové jednotky**

## <a name="update-the-technical-details-of-a-sku"></a>Aktualizovat podrobnosti SKU
Přidání nové verze uvedené SKU a nové publikování vaší nabídky, postupujte podle těchto kroků:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **IMAGÍ virtuálních počítačů** kartu.
4. V **SKU** části, vyhledejte jednotky SKU, které chcete aktualizovat.
5. Přidejte nové číslo verze pro danou skladovou jednotku a klikněte na tlačítko **+** tlačítko. Nová verze musí být ve formátu X.Y.Z, kde X, Y a jsou celá čísla. By měly být jenom přírůstkové změny verze.
6. V **URL virtuálního pevného disku operačního systému** zadejte sdílený přístupový podpis pro operační systém virtuální pevný disk vytvořit identifikátor URI a uložte změny.

   > [!IMPORTANT]
   > Je nelze přírůstek a snížení počet datových disků uvedené skladové položky. Je potřeba vytvořit novou skladovou Položku v tomto případě. Podrobné pokyny najdete v části [přidat novou skladovou Položku v seznamu nabídky](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Přejděte **publikovat** kartu a klikněte na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**. Podrobné informace o testování vaší nabídky v testovacím prostředí, najdete v části [testování vaší nabídky virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po otestování vaší nabídky v testovacím prostředí přejděte **publikovat** kartu v publikování portál. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

    ![Image virtuálních počítačů](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Aktualizovat běžné uživatele podrobnosti nabídky nebo SKU
Můžete aktualizovat běžné uživatele (marketingu, právní, podpora, kategorie) podrobnosti živé nabídky a skladové položky na webu Marketplace.

### <a name="update-the-offer-description-and-logos"></a>Aktualizovat popis nabídky a loga
Chcete-li aktualizovat podrobnosti o nabídce a nové publikování vaší nabídky, postupujte podle těchto kroků:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **marketingové** kartu.
4. Klikněte na tlačítko **Angličtina (USA)**.
5. Klikněte na tlačítko **podrobnosti** kartu. V **popis** části, aktualizujte tuto nabídku **TITLE**, **Souhrn**, a **dlouhé shrnutí** a uložte změny.

   > [!NOTE]
   > Když aktualizujete podrobné údaje SKU, mějte na paměti tato omezení: 
   * Nezadávejte duplicitní text pro popis nabídky a skladové položky popis.
   * Nezadávejte duplicitní text pro název SKU a nabídky, dlouhé shrnutí. 
   * Nezadávejte duplicitní text pro název SKU a souhrn nabídky.
   >
   >

    ![Podrobnosti](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. V **loga** část **podrobnosti** kartu, aktualizujte loga. Ujistěte se, že loga postupujte [pokyny na webu Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > Ikona hero je volitelný. Můžete nechcete nahrát ikonu hero. Po nahrání se ikona hero neexistuje však žádný zřízení k jeho odstranění z publikování portálu. Postupujte podle [hero ikonu pokynů](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Přejděte **publikovat** kartu a klikněte na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**. Podrobné informace o testování vaší nabídky v testovacím prostředí, najdete v části [testování vaší nabídky virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po otestování vaší nabídky v testovacím prostředí přejděte **publikovat** kartu v publikování portál. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

    ![Loga](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Aktualizujte popis skladové položky
Chcete-li aktualizovat podrobnosti SKU a nové publikování vaší nabídky, postupujte podle těchto kroků:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **marketingové** kartu.
4. Klikněte na tlačítko **Angličtina (USA)**.
5. Klikněte na tlačítko **plány** kartu. V **SKU** části, aktualizovat skladovou Položku **TITLE**, **Souhrn**, a **popis** a uložte změny.

   > [!NOTE]
   > Když aktualizujete podrobné údaje SKU, mějte na paměti tato omezení: 
   * Nezadávejte duplicitní text pro popis nabídky a skladové položky popis. 
   * Nezadávejte duplicitní text pro název SKU a nabídky, dlouhé shrnutí. 
   * Nezadávejte duplicitní text pro název SKU a souhrn nabídky.
   >
   >
6. Přejděte **publikovat** kartu a klikněte na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**. Podrobné informace o testování vaší nabídky v testovacím prostředí, najdete v části [testování vaší nabídky virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po otestování vaší nabídky v testovacím prostředí přejděte **publikovat** kartu v publikování portál. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

    ![Plány](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Změnit existující propojení nebo přidat nové odkazy
Chcete-li změnit existující propojení nebo přidat nové odkazy a pak znovu publikovat vaši nabídku, postupujte podle těchto kroků:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **marketingové** kartu.
4. Klikněte na tlačítko **Angličtina (USA)**.
5. Klikněte na tlačítko **odkazy** kartu.
6. Přidat nový odkaz **odkazy** klikněte na tlačítko **+ přidat odkaz**. V **přidat odkaz** dialogového okna zadejte odkaz **TITLE** a **URL** a uložte změny. Můžete zadat libovolný odkaz, který obsahuje informace, které můžou zákazníkům pomoct.
7. Aktualizovat nebo odstranit existující vazbu, vyberte odkaz a klikněte na tlačítko **upravit** tlačítko nebo **odstranit** tlačítko.

   > [!NOTE]
   > Ujistěte se, že odkazy, které jste zadali v této části fungují správně, protože ověřovat tyto odkazy během zpracování žádosti o vaše produkční.
   >
   >
8. Přejděte **publikovat** kartu a klikněte na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**. Podrobné informace o testování vaší nabídky v testovacím prostředí, najdete v části [testování vaší nabídky virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Po otestování vaší nabídky v testovacím prostředí přejděte **publikovat** kartu v publikování portál. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

    ![Odkazy](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Přidat odkaz](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Změnit existující ukázkový obrázek nebo přidat novou bitovou kopii ukázky
Chcete-li změnit stávající bitovou kopii ukázky nebo přidat nové ukázkové obrázky a pak znovu publikovat vaši nabídku, postupujte podle těchto kroků:

> [!NOTE]
> Pouze jeden vzorek image se zobrazí v [webu Azure portal](https://portal.azure.com).
>
>

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **marketingové** kartu.
4. Klikněte na tlačítko **Angličtina (USA)**.
5. Klikněte na tlačítko **UKÁZKOVÉ obrázky** kartu.
6. Přidat novou bitovou kopii ukázky, **ukázkové obrázky** klikněte na tlačítko **NAHRAJTE nový OBRÁZEK** a uložte změny.

   > [!NOTE]
   > Včetně Ukázkový obrázek je volitelný.
   >
7. Přejděte **publikovat** kartu a klikněte na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**. Podrobné informace o testování vaší nabídky v testovacím prostředí, najdete v části [testování vaší nabídky virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po otestování vaší nabídky v testovacím prostředí přejděte **publikovat** kartu v publikování portál. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

    ![Ukázkové obrázky](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>Aktualizovat obsah platný
Chcete-li aktualizovat platný obsah a nové publikování vaší nabídky, postupujte podle těchto kroků:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **marketingové** kartu.
4. Klikněte na tlačítko **Angličtina (USA)**.
5. Klikněte na tlačítko **právní** kartu. V **právní** části, aktualizujte svoje zásady/podmínky použití. Zadejte nebo vložte zásady/podmínky v **TERMS OF USE** pole a uložení změn.
6. Omezení počtu znaků pro právní podmínky použití je 1 milion znaků.
7. Přejděte **publikovat** kartu a klikněte na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**. Podrobné informace o testování vaší nabídky v testovacím prostředí, najdete v části [testování vaší nabídky virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po otestování vaší nabídky v testovacím prostředí přejděte **publikovat** kartu v publikování portál. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

    ![Právní informace](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Aktualizace informací o podpoře
Chcete-li aktualizovat informace o podpoře a nové publikování vaší nabídky, postupujte podle těchto kroků:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **podporu** kartu.
4. V **Engineering kontaktovat** části, aktualizujte technických podrobností o kontaktu. Tyto údaje se používají pouze pro vnitřní komunikace mezi partnerem a Microsoftem.
5. V **zákaznickou podporu** části, aktualizovat kontaktní údaje podpory a **PODPORUJÍ URL**. Tyto údaje se používají pouze pro vnitřní komunikace mezi partnerem a Microsoftem.

   > [!NOTE]
   > Pokud chcete poskytnout pouze e-mailovou podporu, zadejte fiktivní telefonní číslo v **zákaznickou podporu** oddílu. V takovém případě se místo toho používá e-mailu, který jste zadali.
   >
   >
6. Přejděte **publikovat** kartu a klikněte na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**. Podrobné informace o testování vaší nabídky v testovacím prostředí, najdete v části [testování vaší nabídky virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po otestování vaší nabídky v testovacím prostředí přejděte **publikovat** kartu v publikování portál. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

    ![Podpora](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>Aktualizace kategorií
Aktualizace v části Kategorie nabídky a nové publikování vaší nabídky, postupujte podle těchto kroků:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **kategorie** kartu.
4. V **kategorie** části, aktualizujte kategorie pro vaši nabídku a uložte změny. Můžete zvolit až pět kategorií pro galerii Azure Marketplace.
5. Přejděte **publikovat** kartu a klikněte na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**. Podrobné informace o testování vaší nabídky v testovacím prostředí, najdete v části [testování vaší nabídky virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
6. Po otestování vaší nabídky v testovacím prostředí přejděte **publikovat** kartu v publikování portál. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

    ![Kategorie](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Přidat novou skladovou Položku v seznamu nabídky
Přidat novou skladovou Položku nabídky za provozu, postupujte podle těchto kroků:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **SKU** kartu. Pak klikněte na tlačítko **přidejte skladovou jednotku**. 
4. V dialogovém okně zadejte **identifikátor SKU** malými písmeny. Vyberte **používání vlastní licence (BYOL) model fakturace** zaškrtávací políčko, pokud chcete publikovat novou skladovou Položku s fakturační model BYOL. Jinak zrušte zaškrtnutí políčka. Klikněte na tlačítko Vytvořit novou skladovou Položku značky zaškrtnutí. Pokud jste nezvolili fakturační model BYOL, model fakturace se automaticky nastaví na jednu hodinu. Pokud chcete 30denní bezplatnou zkušební verzi pro modelem hodinové fakturace, vyberte **jeden měsíc** pro **je k dispozici bezplatná zkušební verze?** V opačném případě vyberte **ne zkušební**. (**Je k dispozici bezplatná zkušební verze?**  se zobrazí pouze v případě, že jste nevybrali BYOL při vytváření nové SKU.)

   > [!IMPORTANT]
   > **Skrýt tato skladová položka z webu Marketplace, protože by měl koupil(a) vždy pomocí šablony řešení** by měl být **Ano** *pouze* jste schválení pro publikování šablony řešení. V opačném případě tuto možnost by měla být vždy **ne**.
   >
   >
4. V nabídce na levé straně klikněte **IMAGÍ virtuálních počítačů** kartu a přečtěte si novou skladovou Položku, kterou jste vytvořili.
5. Pokud chcete nastavit novou skladovou Položku, přečtěte si [získání certifikace pro vaši image virtuálního počítače](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) pokyny.
6. Chcete-li přidat marketingové materiály pro novou skladovou Položku, naleznete v tématu [Marketplace poskytují marketingové obsah](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Chcete-li přidat informace o cenách pro novou skladovou Položku, naleznete v tématu [nastavení vašich cen](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Přejděte **publikovat** kartu a klikněte na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**. Podrobné informace o testování vaší nabídky v testovacím prostředí, najdete v části [testování vaší nabídky virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Po otestování vaší nabídky v testovacím prostředí přejděte **publikovat** kartu v publikování portál. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

    ![Skladové položky](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Přidejte skladovou jednotku](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Změňte počet datových disků pro uvedené SKU
Je nelze přírůstek a snížení počet datových disků uvedené skladové položky. Je potřeba vytvořit novou skladovou Položku v tomto případě. Podrobné pokyny najdete v části [přidat novou skladovou Položku v seznamu nabídky](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Odstranit uvedené nabídku z webu Marketplace
Různé aspekty, třeba se postarat v případě žádost o odebrání za provozu nabídky. Pokud chcete získat pokyny od týmu podpory a odebrat uvedené nabídku z webu Marketplace, postupujte podle těchto kroků:

1. Lístek podpory na [vytvořit incident](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) stránky.

2. Vyberte **typ problému** jako **správa nabídek**a vyberte **kategorie** jako **úpravy nabídka a skladová položka již v produkčním prostředí**.
3. Odešlete žádost.

Proces odstraňování nabídka/SKU vás provede na tým podpory.

> [!NOTE]
> Nabídka můžete odstranit vždy, i když je ve stavu návrhu (ale ne přípravné nebo produkční). Na **HISTORIE** klikněte na tlačítko **ZAHODIT koncept**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Odstranit uvedené skladovou Položku z Marketplace
Pokud chcete odstranit uvedené skladovou Položku z webu Marketplace, postupujte takto:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).

2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V podokně na levé straně klikněte **SKU** kartu.
4. Vyberte jednotku SKU, kterou chcete odstranit a kliknutím **odstranit** tlačítko.
5. Přejděte **publikovat** kartu v publikování portálu. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** znovu publikovat nabídky na webu Marketplace.
6. Po opětovném publikování nabídky na webu Marketplace, SKU je odstraněn z webu Marketplace a na webu Azure portal.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Odstranit aktuální verze uvedené skladovou Položku z Marketplace
Chcete-li odstranit aktuální verze uvedené SKU z webu Marketplace, postupujte takto: 

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).

2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **IMAGÍ virtuálních počítačů** kartu.
4. Vyberte SKU, jehož aktuální verzi, kterou chcete odstranit a klikněte na tlačítko **odstranit** tlačítko.
5. Přejděte **publikovat** kartu v publikování portálu. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** znovu publikovat nabídky na webu Marketplace.
6. Po nabídky získá znovu publikovat na webu Marketplace, aktuální verze uvedené SKU je odstraněn z webu Marketplace a na webu Azure portal. SKU se potom vrátí zpět na předchozí verzi.

## <a name="revert-the-listing-price-to-production-values"></a>Vrátí seznam cenu s produkčními hodnotami
Pokud chcete vrátit seznam cenu s produkčními hodnotami, postupujte podle těchto kroků:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).
2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **ceny** kartu.
4. Vyberte oblast, jejichž ceny, které chcete obnovit.

    ![Ceny oblastí](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. Skladová jednotka s modelem hodinové fakturace resetujte ceny platné pro všechna jádra, protože jsou v produkčním prostředí pro vybranou oblast. Skladová jednotka s fakturační model BYOL, zpřístupnit SKU v oblasti tak, že vyberete zaškrtávací políčko pro skladovou Položku **dostupnosti skladových položek EXTERNALLY-LICENSED (BYOL)** oddílu.

    ![Cenové modely](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Klikněte na tlačítko **AUTOPRICE ostatních trhů podle ceny ve Spojených státech amerických**.

   > [!NOTE]
   > Popisek tlačítka se může lišit v závislosti na oblasti, kterou jste vybrali. Protože jsme vybrali Spojených států, toto tlačítko má název **AUTOPRICE ostatní trhy založené na ceny v (Spojené státy)**.
   >
   >

    ![Autoprice](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. Na stránce 1 Průvodce Autoprice, zvolte základní trhu a klikněte na tlačítko **šipku** tlačítko.

    ![Základní trhu](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. Na stránce 2, zvolte plány služeb a měřiče (jader) a klikněte na tlačítko **šipku** tlačítko.

    ![Plány služeb a měřiče](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. Na stránce 3, klikněte na tlačítko **přepnout všechny** k výběru všech oblastech. Nebo můžete ručně vybrat zaškrtávací políčka pro konkrétní oblasti. Klikněte na tlačítko **šipku** tlačítko.

    ![Zvolte trhy](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. Na stránce 4, zkontrolujte směnné kurzy a klikněte na **Dokončit**. Průvodce resetuje ceny podle vašeho výběru.

11. Na **ceny** klikněte na tlačítko **zobrazení Souhrn změn**.
    Pro **zobrazit verzi**vyberte **koncept**a pro **porovnat s**vyberte **produkční**. Pokud se zobrazí žádné rozdíly cenových, ceny vrátit zpět na produkčními hodnotami úspěšně.

    ![Zobrazit souhrn a změny](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Přejděte **publikovat** kartu a klikněte na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**. Podrobné informace o testování vaší nabídky v testovacím prostředí, najdete v části [testování vaší nabídky virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
13. Po otestování vaší nabídky v testovacím prostředí přejděte **publikovat** kartu v publikování portál. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

## <a name="revert-the-billing-model-to-production-values"></a>Vrátit modelu účtování až produkčními hodnotami
Chcete-li vrátit modelu účtování až produkčními hodnotami, postupujte takto:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).

2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **SKU** kartu.
4. Klikněte na tlačítko **upravit** tlačítko model fakturace vrátit zpět. V okně, které se otevře, vyberte nebo zrušte **fakturaci a licencování provádí externě od Azure (označuje se také jako používání vlastní licence)** zaškrtávací políčko.

    ![Upravit fakturace](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Postupujte podle kroků v "Vrácení cenu výpis s produkčními hodnotami" v tomto článku.
6. Přejděte **publikovat** kartu a klikněte na tlačítko **NEPŘEJDE k PŘÍPRAVĚ**. Podrobné informace o testování vaší nabídky v testovacím prostředí, najdete v části [testování vaší nabídky virtuálních počítačů pro Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po otestování vaší nabídky v testovacím prostředí přejděte **publikovat** kartu v publikování portál. Klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Vrátí nastavení viditelnosti uvedené skladové položky na hodnotu produkčního prostředí
Pokud chcete vrátit nastavení viditelnosti uvedené skladové položky na hodnotu produkčního prostředí, postupujte takto:

1. Přihlaste se k [portál pro publikování](https://publish.windowsazure.com).

2. Přejděte **virtuálních počítačů** kartu a vyberte vaši nabídku.
3. V nabídce na levé straně klikněte **SKU** kartu.
4. Vyberte vaši skladovou jednotku, a vrátí nastavení viditelnosti skladové položky na hodnotu produkční.

    ![Viditelnost](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Po dokončení změny, klikněte na tlačítko **žádost o schválení pro PUSH do produkčního prostředí** pro nové publikování vaší nabídky na webu Marketplace.

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: Publikování nabídky na webu Azure Marketplace](marketplace-publishing-getting-started.md)
* [Pochopení hlášení výběr](marketplace-publishing-report-payout.md)
* [Změnit váš prodejce incentive poskytovatele Cloud Solution Provider](marketplace-publishing-csp-incentive.md)
* [S řešením běžných problémů publikování na webu Marketplace](marketplace-publishing-support-common-issues.md)
* [Získat podporu jako vydavatel](marketplace-publishing-get-publisher-support.md)
* [Místní vytvoření image virtuálního počítače](marketplace-publishing-vm-image-creation-on-premise.md)
* [Vytvoření virtuálního počítače s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
