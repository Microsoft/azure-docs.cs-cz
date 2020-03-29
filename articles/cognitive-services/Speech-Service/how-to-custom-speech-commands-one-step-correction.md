---
title: 'Postup: Přidání jednokrokové opravy do vlastního příkazu (náhled) – služba řeč'
titleSuffix: Azure Cognitive Services
description: V tomto článku vysvětlujeme, jak implementovat jednokrokové opravy příkazu ve vlastních příkazech.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456451"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Postup: Přidání jednokrokové opravy do vlastního příkazu (náhled)

V tomto článku se dozvíte, jak přidat potvrzení o jednom kroku do příkazu.

Oprava v jednom kroku se používá k aktualizaci právě dokončeného příkazu.

Tj. pokud jste právě nastavit alarm, můžete změnit svůj názor a aktualizovat čas alarmu.

- Vstup: Nastavení budíku na zítřek v poledne
- Výstup: "Ok, budík nastaven na 12/06/2019 12:00:00"
- Vstup: Ne, zítra ve 13 hodin
- Výstup: "Ok

Mějte na paměti, že to znamená, že jako vývojář máte mechanismus pro aktualizaci alarmu v back-endové aplikaci.

## <a name="prerequisites"></a>Požadavky

Musíte mít dokončeny kroky v následujících článcích:

- [Úvodní příručka: Vytvoření vlastního příkazu (náhled)](./quickstart-custom-speech-commands-create-new.md)
- [Úvodní příručka: Vytvoření vlastního příkazu s parametry (náhled)](./quickstart-custom-speech-commands-create-parameters.md)
- [Postup: Přidání potvrzení do vlastního příkazu (náhled)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Přidání rozšířených pravidel pro jednostupňovou opravu 

Chcete-li demonstrovat jednostupňovou korekci, rozšiřte příkaz **SetAlarm** vytvořený v [potvrzovacím příkazu Jak](./how-to-custom-speech-commands-confirmations.md).
 
1. Přidejte rozšířené pravidlo pro aktualizaci předchozího alarmu. 

    Toto pravidlo požádá uživatele o potvrzení data a času alarmu a očekává potvrzení (ano/ne) pro další tah.

   | Nastavení               | Navrhovaná hodnota                                                  | Popis                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Název pravidla             | Aktualizovat předchozí alarm                                            | Název popisující účel pravidla          |
   | Podmínky            | UpdateLastCommand & povinný parametr - DateTime                | Podmínky, které určují, kdy může být pravidlo spuštěno    |   
   | Akce               | SpeechResponse - "- Aktualizace předchozího alarmu na {DateTime}"       | Akce, která má být v případě, že je splněna podmínka pravidla |
   | Stát po provedení | Dokončit, příkaz                                                 | Stav uživatele po otočení                   |

1. Přesuňte pravidlo, které jste právě vytvořili, na začátek rozšířených pravidel (přejděte přes pravidlo v panelu a klepněte na šipku NAHORU).
   > [!div class="mx-imgBorder"]
   > ![Přidání ověření rozsahu](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> V reálné aplikaci v části Akce tohoto pravidla také odešlete zpět aktivitu klientovi nebo zavoláte koncový bod HTTP pro aktualizaci alarmu ve vašem systému.

## <a name="try-it-out"></a>Vyzkoušejte si to.

Vyberte panel Test a vyzkoušejte několik interakcí.

- Vstup: Nastavení budíku na zítřek v poledne
- Výstup: "Opravdu chcete nastavit budík na 12/07/2019 12:00:00?"
- Vstup: Ano
- Výstup: "Ok, budík nastaven na 12/07/2019 12:00:00"
- Vstup: Ne, zítra ve 13 hodin
- Výstup: "Aktualizace předchozího alarmu na 12/07/2019 13:00:00"
