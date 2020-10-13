---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 0fd13437ecd494eebf79fa80ed210a0663864104
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875420"
---
V tomto rychlém startu se naučíte základní vzory návrhu pro rozpoznávání mluvčího pomocí sady Speech SDK, včetně těchto:

* Ověřování závislé na textu a nezávislé na textu
* Identifikace mluvčího pro identifikaci hlasového vzorku mezi skupinou hlasů
* Odstraňování hlasových profilů

Základní informace o konceptech rozpoznávání řeči najdete v článku [Přehled](../../../speaker-recognition-overview.md) .

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Rozpoznávání mluvčího se aktuálně podporuje *jenom* v prostředcích Azure pro rozpoznávání řeči vytvořených v `westus` oblasti.

## <a name="text-dependent-verification"></a>Ověřování závislé na textu

Ověření mluvčího je potvrzení, že mluvčí odpovídá známému nebo **zaregistrovanému** hlasu. Prvním krokem je **zápis** hlasového profilu, aby služba měla něco pro porovnání budoucích ukázek hlasu s. V tomto příkladu zaregistrujete profil s použitím strategie **závislé na textu** , která vyžaduje konkrétní přístupové heslo, které se má použít pro zápis i ověřování. Seznam podporovaných přístupových hesel najdete v [referenční dokumentaci](https://docs.microsoft.com/rest/api/speakerrecognition/) .

Začněte [vytvořením hlasového profilu](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/createprofile). Do každého z příkazů v tomto článku budete muset vložit klíč a oblast předplatného služby Speech.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

Všimněte si, že existují tři typy hlasového profilu:

- Ověřování závislé na textu
- Ověřování nezávislé na textu
- Identifikace nezávislá na textu

V tomto případě vytvoříte profilový hlasový profil závislý na textu. Měla by se zobrazit následující odpověď.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

V dalším kroku [zaregistrujete hlasový profil](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/createenrollment). V poli `--data-binary` hodnota parametru zadejte zvukový soubor v počítači, který obsahuje jedno z podporovaných přístupových hesel, například "můj hlas je můj pas, ověřit mě." Takový zvukový soubor můžete nahrát pomocí aplikace, jako je například [záznam hlasu ve Windows](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab), nebo ho můžete vygenerovat pomocí převodu [textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-text-to-speech).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

Měla by se zobrazit následující odpověď.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

Tato odpověď vám ukáže, že je potřeba zaregistrovat ještě dvě zvukové ukázky.

Po registraci celkem tří ukázek zvuku by se měla zobrazit následující odpověď.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

Nyní jste připraveni [ověřit ukázku zvuku proti profilu hlasu](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/verifyprofile). Tento zvukový vzorek by měl obsahovat stejné přístupové heslo jako ukázky, které jste použili k zápisu hlasového profilu.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

Měla by se zobrazit následující odpověď.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

`Accept`To znamená, že heslo odpovídá a ověření bylo úspěšné. Odpověď obsahuje také skóre podobnosti v rozsahu od 0,0 do 1,0.

Chcete-li dokončit, [odstraňte hlasový profil](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textdependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

Neexistuje žádná odpověď.

## <a name="text-independent-verification"></a>Ověřování nezávislé na textu

Na rozdíl od ověřování **závislého** na textu, ověřování **nezávislého na textu** :

* Nevyžaduje, aby bylo možné některé heslo vymluveným, cokoli se dá přehlasovat.
* Nevyžaduje tři zvukové vzorky *, ale vyžaduje* 20 sekund celkového zvuku.

Začněte [vytvořením nezávislého textového profilu pro ověřování](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

Měla by se zobrazit následující odpověď.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

Pak [Zaregistrujte hlasový profil](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/createenrollment). Místo toho, abyste odeslali tři zvukové vzorky, potřebujete odeslat zvukové vzorky, které obsahují celkem 20 sekund zvukového zvuku.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

Po odeslání dostatečného počtu zvukových ukázek by se měla zobrazit následující odpověď.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

Nyní jste připraveni [ověřit ukázku zvuku proti profilu hlasu](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/verifyprofile). Tato ukázka zvuku zase nemusí obsahovat přístupové heslo. Může obsahovat jakýkoli řeč, pokud obsahuje celkem alespoň čtyři sekundy zvukového zařízení.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

Měla by se zobrazit následující odpověď.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

`Accept`To znamená, že ověření bylo úspěšné. Odpověď obsahuje také skóre podobnosti v rozsahu od 0,0 do 1,0.

Chcete-li dokončit, [odstraňte hlasový profil](https://docs.microsoft.com/rest/api/speakerrecognition/verification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

Neexistuje žádná odpověď.

## <a name="speaker-identification"></a>Identifikace mluvčího

Identifikace mluvčího slouží k určení toho **, kdo** z dané skupiny zaregistrovaných hlasů mluví. Tento proces se podobá **ověřování nezávisle na textu**, s hlavním rozdílem, který je schopný ověřit u několika hlasových profilů najednou, a ne u jednoho profilu.

Začněte [vytvořením nezávislého textu pro identifikaci](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

Měla by se zobrazit následující odpověď.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

V dalším kroku [zaregistrujete hlasový profil](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/createenrollment). Znovu je potřeba odeslat zvukové vzorky, které obsahují celkem 20 sekund zvukového zvuku. Tyto ukázky nemusejí obsahovat přístupové heslo.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

Po odeslání dostatečného počtu zvukových ukázek by se měla zobrazit následující odpověď.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

Nyní jste připraveni [identifikovat zvukový vzorek pomocí hlasového profilu](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker). Příkaz identifikovat přijímá seznam možných ID hlasových profilů oddělený čárkami. V takovém případě pouze předáte ID hlasového profilu, který jste vytvořili dříve. Pokud ale chcete, můžete předat víc ID hlasových profilů, kde každý hlasový profil je zaregistrovaný pomocí zvukového vzorku z jiného hlasu.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

Měla by se zobrazit následující odpověď.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

Odpověď obsahuje ID hlasového profilu, který nejlépe odpovídá vzorku zvuk, který jste odeslali. Obsahuje také seznam kandidátních hlasových profilů seřazený podle podobnosti.

Chcete-li dokončit, [odstraňte hlasový profil](https://docs.microsoft.com/rest/api/speakerrecognition/identification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

Neexistuje žádná odpověď.
