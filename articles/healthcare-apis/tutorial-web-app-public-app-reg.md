---
title: Kurz webové aplikace – instalace klientské aplikace
description: Tento kurz vás provede jednotlivými kroky registrace veřejné aplikace pro připravujení nasazení webové aplikace.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "84872639"
---
# <a name="client-application-registration"></a>Registrace klientské aplikace
V předchozím kurzu jste nasadili a nastavili rozhraní API Azure pro FHIR. Teď, když máte k dispozici rozhraní API Azure pro FHIR, zaregistrujeme veřejnou klientskou aplikaci. Můžete si přečíst kompletní [registraci veřejného klienta aplikace](register-public-azure-ad-client-app.md) průvodce, kde najdete další podrobnosti nebo řešení potíží, ale zjistili jsme hlavní kroky tohoto kurzu.

1. Přejít na Azure Active Directory
1. Vybrat **registraci aplikace**  -->  **Nová registrace**
1. Pojmenujte aplikaci a nastavte identifikátor URI přesměrování nahttps://www.getpostman.com/oauth2/callback


![Registrace klientské aplikace](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>Nastavení klientské aplikace
Po registraci klientské aplikace zkopírujte na stránce Přehled ID aplikace (klienta). Tuto hodnotu budete potřebovat později při přístupu k klientovi.

![Kopírovat ID aplikace](media/tutorial-web-app/app-id.png)

Dále nastavte správné možnosti ověřování. Na levé straně vyberte **ověřování** . Zaškrtněte pole **přístupový token** a **token ID** . Identifikátor URI přesměrování můžete také nastavit v přípravě na při vytváření webové aplikace ve čtvrté části tohoto kurzu. Uděláte to tak, že do \< seznamu identifikátorů URI pro přesměrování přidáte https://Web-App-NAME>. azurewebsites.NET. Pokud při [psaní webové aplikace](tutorial-web-app-write-web-app.md)zvolíte jiný název, budete se muset vrátit a aktualizovat.

![Nastavení ověřování aplikací](media/tutorial-web-app/app-authentication.png)

Teď, když máte nastavené správné ověření, nastavte oprávnění rozhraní API. 
1. Vyberte **oprávnění rozhraní API** a klikněte na **Přidat oprávnění** .
1. V části **rozhraní API, které používá moje organizace**, vyhledejte rozhraní API pro zdravotní péče Azure
1. Vyberte **user_impersonation** a klikněte na **Přidat oprávnění** .

## <a name="next-steps"></a>Další kroky
Nyní máte veřejnou klientskou aplikaci. V dalším kurzu provedeme testování a získáme přístup k této aplikaci prostřednictvím post.

>[!div class="nextstepaction"]
>[Testovat klientskou aplikaci v nástroji post](tutorial-web-app-test-postman.md)
