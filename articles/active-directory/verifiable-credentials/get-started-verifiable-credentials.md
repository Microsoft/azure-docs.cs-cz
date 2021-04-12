---
title: Kurz – Začínáme s Azure Active Directory ověřitelnými přihlašovacími údaji pomocí ukázkové aplikace (Preview)
description: V tomto kurzu se naučíte, jak vystavovat ověřitelné přihlašovací údaje pomocí naší ukázkové aplikace a testovacího tenanta.
ms.service: identity
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ms.openlocfilehash: deebaf31197d8b7335f887ae447f05add45278b2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222881"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>Kurz – Začínáme s Azure Active Directory ověřitelnými přihlašovacími údaji pomocí ukázkové aplikace (Preview)

V tomto kurzu provedeme kroky potřebné k vydání prvního ověřitelných přihlašovacích údajů: na kartě odborníka na ověřené přihlašovací údaje. Tuto kartu pak můžete použít k důkazu ověřovatele, že jste odborníka na ověření přihlašovacích údajů, který je v rámci digitálního přihlašovacích údajů vydaný. Začínáme s Azure Active Directory ověřitelnými přihlašovacími údaji pomocí ukázkové aplikace ověřitelných přihlašovacích údajů k vydání vašich prvních ověřitelných přihlašovacích údajů.

![Toto je obrázek ukázkové karty.](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Active Directory ověřitelných přihlašovacích údajů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

- [NodeJS](https://nodejs.org/en/download/) verze 10,14 nebo novější instalovaná v našem testovacím systému.
- Pokud chcete klonovat úložiště, které hostuje ukázkovou aplikaci, nainstalujte [Git](https://git-scm.com/downloads) .
- [Visual Studio Code](https://code.visualstudio.com/Download)
- Systém pro hostování našeho ukázkového webu.
- Mobilní zařízení s nainstalovanou verzí Microsoft Authenticator 6.2005.3599 nebo vyšší.
- [NGROK](https://ngrok.com/) zdarma.

## <a name="download-the-sample-code"></a>Stažení ukázkového kódu

Pokud chcete vydat přihlašovací kartu odborného pověření pro ověření, musíte na svém místním počítači spustit Web. Web slouží k inicializaci procesu vystavování ověřitelných přihlašovacích údajů. Poskytli jsme jednoduchý web napsaný v NodeJS, který používáme v celém tomto kurzu.

Nejdřív si stáhněte náš ukázkový kód [z GitHubu](https://github.com/Azure-Samples/active-directory-verifiable-credentials)nebo naklonujte úložiště do místního počítače:

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

Možná budete chtít seznámení s kódem na ukázkových webech. `issuer`Složka obsahuje veškerý kód používaný k vystavení ověřitelných přihlašovacích údajů. Další podrobnosti najdete v [souboru Readme](https://github.com/Azure-Samples/active-directory-verifiable-credentials)ukázky.

## <a name="run-the-issuer-website"></a>Spuštění webu vystavitele

Tento postup můžete provést v rámci Visual Studio Code nebo z libovolného příkazového řádku, který je k dispozici v operačním systému. 

1. Přejděte do složky `issuer`. 

    ```terminal
    cd issuer
    ```

2. Až budete potřebovat nainstalovat všechny požadované balíčky a spustit lokalitu.

   ```terminal
    npm install
    node app.js
    ```

3. V terminálu se teď zobrazí, že vaše aplikace vystavitele naslouchá na portu 8081. Teď si nastavíme reverzní proxy s Ngrok, aby ověřovatel mohl komunikovat s vaší aplikací. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Vytvoření reverzního proxy serveru pomocí Ngrok

Když spustíte ukázkový web, vaše zařízení musí komunikovat se serverem uzlů běžícím na vašem místním počítači. Doporučujeme používat [ngrok](https://ngrok.com/) jako snadný způsob zpřístupnění místního vývojového serveru prostřednictvím Internetu.

1.  Po stažení a extrakci **ngrok** musíme spustit:

    ```terminal
     ngrok http 8081
    ```

Ve výchozím nastavení je ukázkový web spouštěn na portu `8081` . **Ngrok** vypíše dvě předávací adresy URL pro váš server. Zkopírujte adresu URL s `https://` předponou.

![ngrok pomáhá zajistit dostupnost koncových bodů aplikace přes Internet.](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> Pokud používáte PowerShell, možná budete muset zadat příkaz, `./ngrok` který se má rozpoznat.

Když je teď váš místní port zpřístupněný pro Internet pomocí ngrok, ukázkový web automaticky používá název hostitele generovaný ngrok. Otevřete prohlížeč a přejděte na adresu URL pro předávání https ngrok. Měli byste být schopni úspěšně navštívit domovskou stránku ukázkové lokality. Pokud se stránka otevře, zařízení může komunikovat s ukázkovou aplikací běžící na vašem místním serveru. Nyní jste připraveni k vystavení na kartě odborného ověřování přihlašovacích údajů.

## <a name="issue-a-credential"></a>Vydání přihlašovacích údajů

1. Nainstalujte ověřovací data do mobilního zařízení. Microsoft Authenticator slouží k přijímání, ukládání a prezentování ověřitelných přihlašovacích údajů zúčastněným stranám.

2. Dále si vydejte ověřené přihlašovací údaje. **Klikněte** na tlačítko **získat pověření** . Když kliknete na tlačítko **získat pověření** , ukázkový web zobrazí kód QR, který můžete kontrolovat pomocí ověřovatele. Pokud si web zobrazíte z prohlížeče na mobilním zařízení, kliknutím na tlačítko **získat pověření** se spustí přímý odkaz, který otevře aplikaci ověřovatele a nevyžaduje skenování kódu QR.

   ![Tlačítko získat pověření](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Naskenujte kód QR webu pomocí ověřovatele nebo pokud k webu přistupujete prostřednictvím mobilního telefonu, klikněte na tlačítko získat pověření a aktivujte přímý odkaz. 

   ![Kód QR ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. Všimněte si, že v tuto chvíli je tlačítko **Přidat** šedé. Klikněte na tlačítko **Přihlásit se k účtu** pod obrázkem karty.

   ![Přihlásit se ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. Předtím, než získáte kartu specialista přihlašovacích údajů, používáme k tomu, abyste zadali ověřovací informace. Pokud procházíte vaším prvním kurzem, že nemáte účet odborníka na přihlašovací údaje, vytvořte nový uživatelský účet v našem tenantovi B2C.

   ![než budete pokračovat, proveďte ověření.](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. Po přihlášení už tlačítko **Přidat** nebude šedé. Zvolením možnosti **Přidat** přijměte svůj nový VC.

   ![Po ověření zvolit přidat](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. Gratulujeme! Teď máte ověřený přihlašovací údaje pro odborníky na přihlašovací údaje.

   ![Přidal se poradce Credential expert VC.](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
Dále je čas ověřit přihlašovací údaje.

## <a name="validate-credentials"></a>Ověřit přihlašovací údaje

Teď, když jste dokončili část vystavení kurzu a máte ověřitelné přihlašovací údaje v ověřovateli, je čas je ověřit ve vaší vlastní aplikaci ověřovatele.

1.  Zastavení spouštění služby vystavitele ngrok.

    ```terminal
     control+c
    ```


2. V jiném okně terminálu otevřete složku aplikace Verifier a spusťte ji podobně jako při spuštění aplikace vystavitele.

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. Nyní spusťte ngrok s portem ověřovatele 8082.

    ```terminal
    ngrok http 8082
    ```

4. V prohlížeči otevřete adresu URL pro předávání https ngrok a klepněte na tlačítko **ověřit přihlašovací údaje** .  

   ![tlačítko ověřit odborníka na přihlašovací údaje](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Otevřete ověřovatel a naskenujte kód QR.

   ![Naskenujte kód QR a ověřte přihlašovací údaje.](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > V systému iOS je vpravo nahoře a na Androidu je to pravý dolní. Naskenujte kód QR.

6. Na stránce Nová žádost oprávnění v okně ověřovatel vyberte možnost **povoleno** . Stisknutím klávesy Allow podepisujete ověřitelnou prezentaci s vaším (decentralizovaným identifikátorem), abyste prokázali, že ve skutečnosti kontrolujete toto ověřitelné přihlašovací údaje.

   ![Nová žádost o oprávnění](media/get-started-verifiable-credentials/new-permission-request.png)

    Po úspěšném dokončení prezentace by se měly aktualizovat tyto tři věci:

   1. Webová stránka by teď měla zobrazovat "Blahopřejeme, vaše jméno" + je ověřený odborník na přihlašovací údaje! ".
   
    ![Blahopřejeme, znovu ověřte](media/get-started-verifiable-credentials/congratulations.png)


   2. Terminál aplikace ověřovatele by taky měl zobrazit stejnou zprávu z protokolů.
   
    ![aktivita aplikace na příkazovém řádku](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. V části ověřovatele by měla existovat položka pro poslední aktivitu této prezentace.

    ![Aktivita v ověřovateli](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> Při spuštění aplikace Verifier může ngrok přestat fungovat a zobrazit chybu, že existuje příliš mnoho připojení. Zjistili jsme, že se to může vyhnout registrací vašeho účtu pomocí ngrok. 

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně dokončili Úvodní příručku, je čas vytvořit vlastní decentralizovaný identifikátor ve službě Azure AD ověřitelných přihlašovacích údajů a vydávat vlastní ověřitelné přihlašovací údaje.

>[!div class="nextstepaction"] 
>[Konfigurace vlastního vystavitele pomocí ukázkové aplikace ověřitelných přihlašovacích údajů](./enable-your-tenant-verifiable-credentials.md)
