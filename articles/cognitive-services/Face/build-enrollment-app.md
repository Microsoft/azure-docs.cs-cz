---
title: Sestavení aplikace s Reakcim pro přidání uživatelů do služby obličeje
titleSuffix: Azure Cognitive Services
description: Naučte se, jak nastavit vývojové prostředí a nasadit aplikaci pro obličeje, abyste získali souhlas od zákazníků.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 39a74c7f3d5fb8f8b60a66947fcce9837ed6ee13
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505101"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>Sestavení aplikace s Reakcim pro přidání uživatelů do služby obličeje

V této příručce se dozvíte, jak začít s ukázkovou aplikací pro registraci obličeje. Tato aplikace předvádí osvědčené postupy pro získání smysluplného souhlasu s cílem přidat uživatele do služby pro rozpoznávání obličeje a získat data s vysokou přesností. Integrovaný systém může použít aplikaci, jako je to, aby poskytoval bez dotykového řízení přístupu, ověření identity, sledování docházky, veřejného terminálu a ověření identity, a to na základě dat jejich obličeje.

Když se spustí aplikace, zobrazí se uživatelům podrobná obrazovka pro vyjádření souhlasu. Pokud uživatel udělí souhlas, aplikace se vyzve k zadání uživatelského jména a hesla a potom zachytí vysoce kvalitní image s použitím kamery zařízení.

Ukázková aplikace je napsaná pomocí JavaScriptu a nativního rozhraní reakce. Teď se dá nasadit na zařízení s Androidem; Další možnosti nasazení přicházejí v budoucnu.

## <a name="prerequisites"></a>Požadavky 

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/).  
* Jakmile budete mít předplatné Azure, vytvořte na Azure Portal [prostředek pro vytváření obličeje](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) a Získejte svůj klíč a koncový bod. Po nasazení vyberte **Přejít k prostředku**.  
  * Pro připojení aplikace k rozhraní API pro rozpoznávání tváře budete potřebovat klíč a koncový bod z prostředku, který jste vytvořili.  
  * Pro místní vývoj a testování můžete vložit klíč rozhraní API a koncový bod do konfiguračního souboru. Pro konečné nasazení uložte klíč rozhraní API do zabezpečeného umístění a nikdy v kódu.  

> [!IMPORTANT]
> Tyto klíče předplatného se používají pro přístup ke službě rozpoznávání rozhraní API. Nesdílejte své klíče. Bezpečně je uložte, například pomocí Azure Key Vault. Tyto klíče doporučujeme také pravidelně obnovovat. K volání rozhraní API je potřeba jenom jeden klíč. Při opětovném generování prvního klíče můžete použít druhý klíč pro pokračování přístupu ke službě.

## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

1. Naklonujte úložiště Git pro [ukázkovou aplikaci](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Chcete-li nastavit vývojové prostředí, použijte nativní dokumentaci reagující na <a href="https://reactnative.dev/docs/environment-setup"  title=" reakci nativní dokumentace "  target="_blank"> </a> . Jako vývojový operační systém vyberte možnost **reagovat nativním rychlým startem CLI** a jako cílový operační systém vyberte **Android** . Dokončete části **instalace závislostí** a **vývojového prostředí Android**.
1. Otevřete env.jsv souboru v upřednostňovaném textovém editoru, jako je například [Visual Studio Code](https://code.visualstudio.com/), a přidejte svůj koncový bod a klíč. Svůj koncový bod a klíč můžete získat na Azure Portal na kartě **Přehled** daného prostředku. Tento krok je jenom pro účely místního testování &mdash; . váš rozhraní API pro rozpoznávání tváře klíč nevraťte se do vzdáleného úložiště.
1. Spusťte aplikaci pomocí emulátoru virtuálního zařízení s Androidem z Android Studio nebo vlastního zařízení s Androidem. Chcete-li otestovat aplikaci na fyzickém zařízení, postupujte podle příslušné <a href="https://reactnative.dev/docs/running-on-device"  title=" reakce nativní dokumentace k reakci v dokumentaci "  target="_blank"> </a> .  


## <a name="create-a-user-add-experience"></a>Vytvoření uživatelského prostředí pro přidání  

Teď, když jste nastavili ukázkovou aplikaci, ji můžete přizpůsobit podle svých potřeb.

Například můžete chtít přidat informace specifické pro konkrétní situaci na stránce s vaším souhlasem:

> [!div class="mx-imgBorder"]
> ![Stránka pro vyjádření souhlasu aplikace](./media/enrollment-app/1-consent-1.jpg)

Služba poskytuje kontroly kvality obrazu, které vám pomůžou vybrat, jestli má image dostatečnou kvalitu pro přidání zákazníka nebo pokus o rozpoznávání obličeje. Tato aplikace ukazuje, jak získat přístup k snímkům z kamery zařízení, vybrat nejvyšší kvalitu snímků a přidat zjištěnou obličej do služby rozhraní API pro rozpoznávání tváře. 

Mnoho problémů s rozpoznáváním tváře je způsobeno nekvalitními referenčními obrázky. Mezi faktory, které můžou snížit výkon modelu, patří:
* Velikost obličeje (plošky, které jsou od kamery vzdálená)
* Orientace obličeje (plošky otočené nebo naklonované z kamery)
* Špatné světelné podmínky (nízké světlo nebo slabé světlo), kde může být obrázek špatně vystavený nebo má příliš velký šum
* Překrytí (částečně skryté nebo zakryté plošky), včetně příslušenství, jako je Hats nebo tlustá orámované brýle)
* Rozostření (například rychlým pohybem obličeje při pořízení fotografie). 

> [!div class="mx-imgBorder"]
> ![Stránka instrukcí pro zachycení image aplikace](./media/enrollment-app/4-instruction.jpg)

Všimněte si, že aplikace také nabízí funkce pro odstranění informací o uživateli a možnost opětovného přidání.

> [!div class="mx-imgBorder"]
> ![Stránka Správa profilů](./media/enrollment-app/10-manage-2.jpg)

Pokud chcete rozšířenou funkci aplikace tak, aby pokryla všechny možnosti, přečtěte si [Přehled](enrollment-overview.md) dalších funkcí k implementaci a osvědčeným postupům.

## <a name="deploy-the-app"></a>Nasazení aplikace

### <a name="android"></a>Android

Nejdřív se ujistěte, že je vaše aplikace připravená na produkční nasazení: Odeberte z kódu aplikace všechny klíče nebo tajné klíče a ujistěte se, že jste postupovali s [osvědčenými postupy zabezpečení](../cognitive-services-security.md?tabs=command-line%2ccsharp).

Až budete připraveni uvolnit aplikaci pro produkční prostředí, vygenerujete soubor APK připravený pro vydání, což je formát souboru balíčku pro aplikace pro Android. Tento soubor APK musí být podepsán pomocí privátního klíče. Pomocí tohoto buildu verze můžete začít distribuovat aplikaci přímo do svých zařízení. 

V <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" dokumentaci Příprava na vydání verze "  target="_blank"> Příprava na vydání </a> se dozvíte, jak vygenerovat privátní klíč, podepsat svoji aplikaci a vygenerovat APK vydání.  

Po vytvoření podepsaného APK si přečtěte dokumentaci k publikování vaší aplikace, <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> kde najdete </a> Další informace o tom, jak aplikaci uvolnit.

## <a name="next-steps"></a>Další kroky  

V této příručce jste zjistili, jak nastavit vývojové prostředí a začít s ukázkovou aplikací. Pokud s dalšími možnostmi reagujete na nativní, můžete si přečíst svoje [dokumenty Začínáme](https://reactnative.dev/docs/getting-started) a získat další informace na pozadí. Také může být užitečné se seznámit s [rozhraní API pro rozpoznávání tváře](Overview.md). Než začnete s vývojem, přečtěte si další části týkající se přidání uživatelů.