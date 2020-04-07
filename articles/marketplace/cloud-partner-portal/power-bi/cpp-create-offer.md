---
title: Vytvoření nabídky aplikace Power BI | Azure Marketplace
description: Jak vytvořit nabídku aplikace Power BI pro tržiště Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 43bb34a80d8d62cfa2bde445b94fb97da25ccda3
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745473"
---
# <a name="create-a-power-bi-app-offer"></a>Vytvoření nabídky aplikace Power BI

>[!Important]
>března 2020 začneme přesouvat správu nabídek aplikací Power BI do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Podle pokynů v [přehledu vytváření aplikací Power BI](https://aka.ms/AzureCreatePBIServiceApp) spravujte migrované nabídky.

V tomto článku se uvádí postup, jak vytvořit novou nabídku aplikace Power BI pro [AppSource](https://appsource.microsoft.com). Každá nabídka se zobrazí jako jeho vlastní entita v AppSource. Když vytvoříte novou nabídku na [portálu partnerů](https://cloudpartner.azure.com/)cloudu , budete muset zadat čtyři skupiny prostředků pro vaši nabídku.

Skupiny majetku jsou popsány v následující tabulce:

|   Skupina majetku      | Popis                                                                         |
| ----------------   | ----------------                                                                    |
| Nastavení nabídky     | Primární identifikace a název nabídky.                                      |
| Technické informace     | Adresa URL instalačního programu, kterou používáte k instalaci aplikace do klientského pracovního prostoru Power BI. Další informace o tom, jak tuto adresu URL vygenerovat, najdete v [dokumentaci k aplikacím Power BI](https://go.microsoft.com/fwlink/?linkid=2028636). |
| Podrobnosti o výloze | Obsahuje marketingové, právní a olovo správcovských prostředků. Marketingové prostředky zahrnují popis nabídky a loga. Právní majetek zahrnuje zásady ochrany osobních údajů, podmínky použití a další právní dokumentaci. Zásady správy zájemců umožňují určit, jak zacházet s zájemci z uživatelského portálu AppSource. |
| Kontakty           | Obsahuje informace o kontaktech podpory a zásadách.                                     |

## <a name="new-offer-form"></a>Formulář nové nabídky

Po přihlášení k portálu partnerů cloudu vyberte v levém podokně **novou nabídku.** Chcete-li zobrazit formulář Nová nabídka a zahájit proces definování datových zdrojů pro novou nabídku aplikace, vyberte **možnost Aplikace Power BI**.

![Položka nabídky Power BI](./media/new-offer-menu.png)

> [!NOTE] 
> Pokud možnost **Aplikace Power BI** není zobrazená nebo není povolená, váš účet nemá oprávnění k vytvoření tohoto typu nabídky. Zkontrolujte, zda jste splnili všechny [požadavky](./cpp-prerequisites.md) pro tento typ nabídky, včetně registrace vývojářského účtu.


## <a name="next-steps"></a>Další kroky

Následující články vysvětlují karty na stránce **Nová nabídka** pro typ nabídky aplikace Power BI. Každý článek popisuje skupiny prostředků a podpůrné služby pro novou nabídku aplikace Power BI.

-  [Karta Nastavení nabídky](./cpp-offer-settings-tab.md)
-  [Karta Technické informace](./cpp-technical-info-tab.md)
-  [Karta podrobností o prodejních místech](./cpp-storefront-details-tab.md)
-  [Karta Kontakty](./cpp-contacts-tab.md)
