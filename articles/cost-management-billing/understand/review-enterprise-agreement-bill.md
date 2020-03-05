---
title: Kontrola informací na faktuře za smlouvu Azure Enterprise
description: Zjistěte, jak číst informace o využití a fakturu za smlouvy Azure Enterprise a jak je chápat.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: banders
ms.openlocfilehash: ca1064ce3414f4a796110c022125b68c0674e0d6
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270189"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Vysvětlení informací na faktuře za smlouvu Azure Enterprise

Když zákazníci Azure se smlouvou Enterprise překročí kredit své organizace nebo použijí služby, na které se kredit nevztahuje, dostanou fakturu.

Kredit vaší organizace zahrnuje váš peněžní závazek. Peněžní závazek je množství, které vaše organizace předem zaplatila za využívání služeb Azure. Pokud chcete do své smlouvy Enterprise přidat prostředky peněžního závazku, obraťte se na svého správce účtu Microsoft nebo prodejce.

Tento kurz se týká zákazníků Azure, kteří mají smlouvu Azure Enterprise.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zkontrolovat fakturované poplatky
> * Zkontrolovat poplatky za nadlimitní využití služby
> * Zkontrolovat fakturu za Marketplace

## <a name="prerequisites"></a>Požadavky

Pokud chcete zkontrolovat a ověřit poplatky na své faktuře, musíte být podnikovým správcem. Další informace najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](../manage/understand-ea-roles.md). Pokud nevíte, kdo je ve vaší organizaci podnikovým správcem, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Kontrola fakturovaných poplatků pro většinu zákazníků

Tato část se netýká zákazníků Azure v Austrálii, Japonsku a Singapuru.

Pokud během fakturačního cyklu dojde k některé z následujících situací, dostanete fakturu za Azure:

- **Nadlimitní využití služby:** Poplatky za využití pro vaši organizace překročí váš kredit.
- **Poplatky účtované zvlášť:** Služby použité vaší organizací, na které se nevztahuje kredit. Bez ohledu na zůstatek vašeho kreditu se vám budou fakturovat následující služby:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registrovaný uživatel
    - Openlogic
    - Registrovaný uživatel s oprávněním ke vzdálenému přístupu XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (měsíční)
    - Visual Studio Enterprise (roční)
    - Visual Studio Professional (měsíční)
    - Visual Studio Professional (roční)
- **Poplatky za Marketplace:** Kredit vaší organizace se nevztahuje na nákupy na webu Azure Marketplace a jejich využívání. Poplatky za Marketplace se vám tedy naúčtují bez ohledu na zůstatek vašeho kreditu. Podnikový správce může povolit nebo zakázat nákupy na webu Marketplace na webu Enterprise Portal.

Na vaší faktuře se zobrazí poplatky za využití Azure s náklady, které jsou k nim přidružené, a za nimi následují případné poplatky za marketplace. Pokud máte kreditní zůstatek, použije se na využití Azure. Na faktuře se uvede využití Azure a využití marketplace bez jakýchkoli nákladů.

V části **Sestavy** > **Souhrn využití** můžete porovnat souhrnnou celkovou částku uvedenou na webu Enterprise Portal s vaší fakturou za Azure. Částky v části **Souhrn využití** jsou uvedené bez daně.

Přihlaste se k [portálu Azure EA](https://ea.azure.com). Vyberte **Reports** (Sestavy). V pravém horním rohu karty přepněte zobrazení z **M** na **C** a nastavte stejné období jako na faktuře.  

![Snímek obrazovky zobrazující možnost M + C v souhrnu využití](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Souhrnná částka za **Celkové využití** a **Azure Marketplace** by měla odpovídat hodnotě **Celková rozšířená částka** na vaší faktuře. Další podrobnosti o vašich poplatcích najdete v části **Stažení dat o využití**.  

![Snímek obrazovky s kartou pro stažení dat o využití](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Kontrola fakturovaných poplatků pro ostatní zákazníky

Tato část se týká jenom zákazníků Azure v Austrálii, Japonsku a Singapuru.

Když nastane některá z následujících situací, dostanete jednu nebo více faktur Azure:

- **Nadlimitní využití služby:** Poplatky za využití pro vaši organizace překročí váš kredit.
- **Poplatky účtované zvlášť:** Služby použité vaší organizací, na které se nevztahuje kredit. Fakturují se vám následující služby:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registrovaný uživatel
    - Openlogic
    - Registrovaný uživatel s oprávněním ke vzdálenému přístupu XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (měsíční)
    - Visual Studio Enterprise (roční)
    - Visual Studio Professional (měsíční)
    - Visual Studio Professional (roční)
- **Poplatky za Marketplace:** Kredit vaší organizace se nevztahuje na nákupy na webu Azure Marketplace a jejich využívání a tyto nákupy a využívání se budou fakturovat zvlášť. Podnikový správce může povolit nebo zakázat nákupy na webu Marketplace na webu Enterprise Portal.

Pokud se vám během jednoho fakturačního období naúčtují poplatky za nadlimitní využití služeb a poplatky účtované zvlášť, dostanete jednu fakturu. Ta bude obsahovat oba typy poplatků. Poplatky za Marketplace se vždycky fakturují samostatně.

## <a name="review-service-overage-charges-for-other-customers"></a>Kontrola poplatků za nadlimitní využití služby pro ostatní zákazníky

Tato část se vás týká jenom v případě, že se nacházíte v Austrálii, Japonsku nebo Singapuru.

V části **Sestavy** > **Souhrn využití** můžete porovnat souhrnnou celkovou částku za využití uvedenou na webu Enterprise Portal s vaší fakturou za nadlimitní využití služby. Faktura za nadlimitní využití služby zahrnuje využití, které překračuje kredit vaší organizace, a služby, na které se kredit nevztahuje. Částky v části **Souhrn využití** jsou uvedené bez daně.

Přihlaste se k [portálu Azure EA](https://ea.azure.com) a potom vyberte **Reports** (Sestavy). V pravém horním rohu karty přepněte zobrazení z **M** na **C** a nastavte stejné období jako na faktuře.  

![Snímek obrazovky zobrazující možnost M + C v souhrnu využití](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Částka **Celkové využití** by měla odpovídat hodnotě **Celková rozšířená částka** na vaší faktuře za nadlimitní využití služby. Další informace o poplatcích najdete v části **Stažení dat o využití** > **Stažení rozšířené sestavy**. Tato sestava nezahrnuje daně, poplatky za rezervace ani poplatky za Marketplace.  

![Snímek obrazovky ukazující možnost stažení rozšířené sestavy na kartě pro stažení dat o využití](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

Následující tabulka obsahuje výrazy uvedené na faktuře a v části **Souhrn využití** na webu Enterprise Portal a jejich popisy:

|Výraz na faktuře|Výraz v souhrnu využití|Popis|
|---|---|---|
|Celková rozšířená částka|Celkové využití|Celkový poplatek za využití před zdaněním za konkrétní období před uplatněním kreditu.|
|Využití v rámci závazku|Využití v rámci závazku|Kredit uplatněný za dané období.|
|Celkový prodej|Celkové nadlimitní využití|Celkový poplatek za využití, které překračuje částku kreditu. Tato částka nezahrnuje daň.|
|Částka daně|Neuvedeno|Daň, která se uplatňuje na částku celkového prodeje za dané období.|
|Celková částka|Neuvedeno|Splatná částka faktury po uplatnění kreditu a přičtení daně.|

### <a name="review-marketplace-invoice"></a>Kontrola faktury za Marketplace

Tato část se vás týká jenom v případě, že se nacházíte v Austrálii, Japonsku nebo Singapuru.

V části **Sestavy** > **Souhrn využití** na portálu Enterprise Portal můžete porovnat celkovou částku za Azure Marketplace s vaší fakturou za Marketplace. Faktura za marketplace je pouze za nákupy a využití na Azure Marketplace. Částky v části **Souhrn využití** jsou uvedené bez daně.

Přihlaste se k [portálu Enterprise](https://ea.azure.com) a potom vyberte **Reports** (Sestavy). V pravém horním rohu karty přepněte zobrazení z **M** na **C** a nastavte stejné období jako na faktuře.  

![Snímek obrazovky zobrazující možnost M + C v souhrnu využití](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Celková částka za **Azure Marketplace** by měla odpovídat hodnotě **Celkový prodej** na vaší faktuře za Marketplace. Další informace o účtovaných poplatcích podle využití najdete v části **Stažení dat o využití**. V části **Poplatky za Marketplace** vyberte **Stáhnout**. Cena za Marketplace zahrnuje daň, jak stanovuje vydavatel. Zákazníci nedostanou samostatnou fakturu od vydavatele pro shromáždění daně transakce.

![Snímek obrazovky s možností stažení v části poplatků za Marketplace](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zkontrolovat fakturované poplatky
> * Zkontrolovat poplatky za nadlimitní využití služby
> * Zkontrolovat fakturu za Marketplace

Další informace o použití portálu Azure EA najdete v dalším článku.

> [!div class="nextstepaction"]
> [Začínáme s portálem Azure EA](../manage/ea-portal-get-started.md)
