---
title: Vysvětlení konceptů úložiště modelu Azure IoT | Microsoft Docs
description: Jako vývojář řešení nebo odborníky v IT se dozvíte o základních konceptech úložiště modelu Azure IoT.
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c82858294054b50d6edae42a3d41e9fcb89ca89d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577794"
---
# <a name="azure-iot-model-repository"></a>Úložiště modelu Azure IoT

Úložiště modelu Azure IoT umožňuje tvůrcům zařízení spravovat a sdílet modely zařízení IoT technologie Plug and Play. Modely zařízení jsou definice dokumentů JSON LD, které jsou definované pomocí [DTDL (Digital vlákna Modeling Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Modely uložené ve službě úložiště modelů se dají sdílet s vývojáři řešení soukromě prostřednictvím řízení přístupu nebo veřejně, aniž by museli k integraci a vývoji cloudového řešení IoT technologie Plug and Play použít jakékoli ověřování.

> [!NOTE]
> Tvůrci zařízení se můžou rozhodnout implementovat modely IoT technologie Plug and Play zařízení přímo na zařízení, používat moduly nebo v modulu IoT Edge.

K úložišti modelu můžete přistupovat pomocí:

- Portál [Azure IoT model úložiště](https://aka.ms/iotmodelrepo)
- [REST API úložiště modelu Azure IoT](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Příkazy úložiště modelu IoT Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest&preserve-view=true)

## <a name="public-models"></a>Veřejné modely

Veřejné digitální dostupné modely uložené v úložišti modelu jsou dostupné všem, aby je bylo možné využívat a integrovat do jejich aplikace bez ověřování. Veřejné modely navíc umožňují otevřenému ekosystému zařízení a vývojářům řešení sdílet a znovu používat svoje modely zařízení technologie Plug and Play IoT.

V části [publikování modelu](#publish-a-model) v části **modely společnosti** najdete pokyny k tomu, jak publikovat model v úložišti modelu, aby byl veřejný.

Postup zobrazení veřejného modelu pomocí portálu úložiště modelů:

1. Přejít na [portál úložiště modelu Azure IoT](https://aka.ms/iotmodelrepo).

1. Vyberte možnost **Zobrazit veřejné modely**.

    ![Zobrazit veřejné modely](./media/concepts-model-repository/public-models.png)

Chcete-li zobrazit veřejný model prostřednictvím REST API, přečtěte si téma [získání modelu](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API dokumentaci.

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-09-30").ConfigureAwait(false);
```

Pokud chcete zobrazit veřejný model pomocí rozhraní příkazového řádku, přečtěte si příkaz Azure CLI [Get model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) .

## <a name="company-models"></a>Modely společnosti

Úložiště podnikového modelu je tenant v úložišti modelu Azure IoT, ve kterém může vaše organizace vytvářet a spravovat digitální zdvojené modely vytvořené uživateli v rámci vaší společnosti nebo organizace. Modely společnosti jsou dostupné jenom pro ověřené uživatele vaší společnosti nebo organizace. Správce tenanta úložiště modelu může přiřadit oprávnění a řídit přístup dalších uživatelů společnosti nebo organizace k modelům v úložišti podnikového modelu.

### <a name="set-up-your-company-model-repository"></a>Nastavení úložiště podnikového modelu

Pro přístup k úložišti modelu použijte svůj *pracovní nebo školní účet Azure Active Directory (Azure AD)* . Pokud už má vaše organizace tenant služby Azure AD, můžete použít uživatelské účty a instanční objekty z tohoto tenanta Azure AD.

Informace o tom, jak nastavit tenanta Azure AD a jak vytvořit uživatele nebo instanční objekt v tenantovi Azure AD, najdete v části [Další informace](#additional-information) .

- Pokud jste prvním uživatelem vaší organizace, abyste měli přístup k úložišti modelu nebo se přihlásili na portál, budete mít **Správce klienta** roli. Tato role umožňuje přiřadit role jiným uživatelům v tenantovi úložiště vaší organizace.

- Jiným rolím můžete přiřadit **Správce klienta** , jako jsou **čtení modelů** nebo **vytváření modelů**.

### <a name="understand-access-management"></a>Pochopení správy přístupu

Následující tabulka shrnuje podporované možnosti v úložišti podnikového modelu a jejich přidružená oprávnění:

| Schopnost  | Oprávnění| Description|
|-------------|-----------|------------|
|Čtení modelů|Čtení modelů|Ve výchozím nastavení mohou všichni uživatelé v tenantovi společnosti zobrazit své firemní modely. Kromě toho může uživatel zobrazit také soukromé modely, které jim sdílely jiné společnosti.|
|Správa přístupu|Správa přístupu|Správa přiřazení role uživatele (přidání nebo odebrání) pro ostatní uživatele v organizaci.|
|Vytváření modelů|Vytváření modelů|Vytvářejte modely v úložišti podnikového modelu.|
|Publikování modelů|Publikování modelů|Publikujte modely, abyste je mohli zveřejnit pro kohokoli, aby si model viděli.|

Následující tabulka shrnuje podporované role a jejich schopnosti v úložišti modelů, které lze použít pro správu přístupu.

|Role|Schopnost|
|----|----------|
|TenantAdministrator|Správa přístupu, čtení modelů|
|tvůrce|Vytváření modelů, čtení modelů|
|Publisher|Publikování modelů, čtení modelů|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>Předání tokenu zabezpečení při přístupu k podnikovým modelům pomocí REST API

Když zavoláte rozhraní REST API pro správu podnikových modelů, které jsou soukromé nebo sdílené, musíte pro uživatele nebo instanční objekt ve formátu JWT zadat autorizační token. V části [Další informace](#additional-information) se dozvíte, jak získat token JWT pro uživatele nebo instanční objekt.

Token JWT se musí předat v hlavičce autorizačního protokolu HTTP v rozhraní API při cílení na modely společnosti nebo sdílené modely. Token JWT není potřebný při cílení na veřejné modely.

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>Zobrazit společnost nebo sdílené modely

Musíte být členem role *Čtenář* tenanta úložiště, jinak se musí model sdílet s vámi, aby bylo možné číst model. Můžete zobrazit seznam nepublikovaných modelů, které s vámi někdo sdíleli, a seznam publikovaných modelů, které s vámi někdo sdílí. Ve výchozím nastavení mohou uživatelé číst své firemní modely, modely, které s nimi sdíleli jiní výrobci, a všechny veřejné modely.

Chcete-li zobrazit společnost nebo sdílený model pomocí portálu:

1. Přihlaste se k [portálu úložiště modelu Azure IoT](https://aka.ms/iotmodelrepo).

1. Rozbalení **modelů společnosti** – zrušení **publikování** v levém podokně

    ![Zobrazit modely společnosti](./media/concepts-model-repository/view-company-models.png)

1. Rozbalte položku **sdílené modely – zrušit publikování** v levém podokně.

    ![Zobrazit sdílené modely](./media/concepts-model-repository/view-shared-models.png)

Chcete-li zobrazit společnost nebo sdílený model pomocí REST API, přečtěte si dokumentaci k [získání modelu](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API. Informace o tom, jak předat autorizační hlavičku JWT v žádosti HTTP, najdete v tématu [předání tokenu zabezpečení při přístupu k podnikovým modelům pomocí REST API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) .

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-09-30").ConfigureAwait(false);
```

Postup zobrazení modelu společnosti nebo sdíleného modelu pomocí rozhraní příkazového řádku najdete v příkazu Azure CLI [Get model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) .

### <a name="manage-roles"></a>Správa rolí

Správce klienta může přiřadit role uživatelům v tenantovi úložiště, aby mohli vytvářet modely privátní pro společnost nebo organizaci, publikovat modely nebo spravovat role pro ostatní uživatele.

Postup přidání uživatele do role tenanta úložiště modelu pomocí portálu:

1. Přihlaste se k [portálu úložiště modelu Azure IoT](https://aka.ms/iotmodelrepo).

1. V levém podokně vyberte **Správa přístupu** a pak vyberte **+ Přidat**. V podokně **Přidat oprávnění** zadejte pracovní adresu uživatele, kterého chcete přidat do role:

    ![Přidat pracovní adresu](./media/concepts-model-repository/add-user.png)

1. Z rozevíracího seznamu **role** vyberte roli, do které chcete uživatele přidat. Pak vyberte **Uložit**:

    ![Zvolit roli](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>Nahrání modelu

Abyste mohli nahrát model do úložiště podnikového modelu, musíte být členem role **Tvůrce** tenanta úložiště.

Tyto modely nejsou publikované a jsou dostupné jenom uživatelům v organizaci ve výchozím nastavení. Můžete také sdílet jeden nebo více nepublikovaných modelů s externími uživateli.

Nahrané modely jsou neměnné.

Identifikátory modelů pro tyto modely musí být globálně jedinečné napříč všemi klienty úložiště pro všechny nahrané modely.

Postup nahrání modelu pomocí portálu:

1. Přihlaste se k [portálu úložiště modelu Azure IoT](https://aka.ms/iotmodelrepo).

1. V levém podokně rozbalte **modely společnosti** a vyberte **vytvořit model**. Pak vyberte **importovat JSON**.

    ![Vytvořit model](./media/concepts-model-repository/create-model.png)

1. Vyberte soubor, který chcete odeslat. Pokud portál úspěšně ověřuje váš model, vyberte **Uložit**.

Pokud chcete nahrát model pomocí REST API, přečtěte si téma Vytvoření rozhraní API [modelu](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) . Informace o tom, jak předat autorizační hlavičku JWT v žádosti HTTP, najdete v tématu [předání tokenu zabezpečení při přístupu k podnikovým modelům pomocí REST API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) .

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-09-30", httpContent).ConfigureAwait(false);
```

Postup nahrání modelu pomocí rozhraní příkazového řádku najdete v tématu příkaz Azure CLI [Create a model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create&preserve-view=true) .

### <a name="publish-a-model"></a>Publikování modelu

Chcete-li publikovat model, musí být splněny následující požadavky:

1. Aby bylo možné publikovat model, musí být vaše organizace členem [Microsoft Partner Network](https://docs.microsoft.com/partner-center/) . Pokud chcete vytvořit účet partnerského centra, přečtěte si téma [Vytvoření účtu partnerského centra](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account). Po schválení účtu můžete publikovat své modely. Další informace najdete v tématu [Nejčastější dotazy k partnerskému centru](https://support.microsoft.com/help/4340639/partner-center-account-faqs).

2. Uživatel musí být členem role *vydavatele* tenanta úložiště.

Modely, které jsou vytvořené a publikované uživateli v rámci vaší organizace, jsou viditelné jako *publikované modely*. Tyto modely jsou veřejné a můžou je najít kdokoli v části **veřejné modely**.

Publikování modelu pomocí portálu:

1. Přihlaste se k [portálu úložiště modelu Azure IoT](https://aka.ms/iotmodelrepo).

2. V levém podokně rozbalte **modely společnosti** a vyberte model, který chcete publikovat. Potom vyberte **Publikovat**.

    ![Publikování modelu](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> Pokud se zobrazí oznámení oznamující, že nemáte ID partnera Microsoftu (MPN), postupujte podle pokynů k registraci v oznámení. Další informace najdete v požadavcích na začátku této části.

Postup publikování modelu pomocí REST API naleznete v dokumentaci k [publikování modelu](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) REST API. Zadejte parametr řetězce dotazu `update-metadata=true` pro publikování modelu pomocí REST API. Informace o tom, jak předat autorizační hlavičku JWT v žádosti HTTP, najdete v tématu [předání tokenu zabezpečení při přístupu k podnikovým modelům pomocí REST API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) .

Pokud chcete publikovat model pomocí rozhraní příkazového řádku, přečtěte si příkaz Azure CLI [publikování modelu](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish&preserve-view=true) .

> [!NOTE]
> Než spustíte testy certifikace, musí být modely publikované v úložišti modelu. Další informace najdete v tématu [jak certifikovat technologie Plug and Play zařízení IoT](howto-certify-device.md).

### <a name="share-a-model"></a>Sdílení modelu

Můžete sdílet firemní modely, které jste vytvořili s uživateli externích organizací. Tímto způsobem můžete spolupracovníkům dovolit zobrazit a vyvíjet řešení pomocí vašich privátních firemních modelů.

Výrobce zařízení může například chtít zachovat modely, které jsou pro společnost nebo organizaci privátní. Můžou mít zákazníky, kteří vyžadují, aby jejich schopnosti zařízení zůstaly důvěrné.

Sdílení modelů napříč společnostmi nebo organizacemi umožňuje zabezpečený přístup k modelům, které nejsou veřejné.

Sdílení modelu společnosti pomocí portálu:

- Pokud jste autorem modelu, jsou při zobrazení modelu v části **modely společnosti** aktivní **sdílená** tlačítka a **sdílená s** tlačítky.

    ![Model sdílení](./media/concepts-model-repository/share-model.png)

- Pokud chcete model sdílet s externím uživatelem, vyberte **sdílet**. V podokně **sdílet model** zadejte e-mailovou adresu externího uživatele a vyberte **Uložit**.

- Chcete-li zobrazit uživatele, se kterými jste model sdíleli, vyberte možnost **sdílet s**.

- Pokud chcete ukončit sdílení modelu s konkrétním uživatelem, vyberte uživatele ze seznamu uživatelů v podokně **sdíleno s** . Po zobrazení výzvy vyberte **Odebrat** a potvrďte volbu.

    ![Ukončit sdílení](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>Další informace

Při práci se službou Azure AD můžete najít následující témata:

- Pokud chcete vytvořit nového tenanta Azure AD, přečtěte si téma [Vytvoření nového tenanta ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant). Většina organizací už bude mít tenanta Azure AD.

- Pokud chcete přidat uživatele nebo uživatele typu Host do tenanta služby Azure AD, přečtěte si téma [Přidání nebo odstranění uživatelů pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

- Pokud chcete přidat instanční objekt k tenantovi služby Azure AD, přečtěte si téma [jak použít portál k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

- Informace o tom, jak získat token JWT z Azure AD, který se má použít při volání rozhraní REST API, najdete v tématu [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app).

## <a name="next-steps"></a>Další kroky

Doporučeným dalším krokem je kontrola [architektury IoT technologie Plug and Play](concepts-architecture.md).
