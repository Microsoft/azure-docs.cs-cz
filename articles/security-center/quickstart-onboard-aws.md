---
title: Připojte svůj účet AWS k Azure Security Center
description: Monitorování prostředků AWS z Azure Security Center
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ee1b8fe6ed97f3b71cda418cce9e432f7c045447
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936534"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Připojení účtů AWS k Azure Security Center

Cloudové úlohy běžně pokrývá několik cloudových platforem, ale cloudové služby zabezpečení musí provádět stejné.

Azure Security Center chrání úlohy v Azure, Amazon Web Services (AWS) a Google Cloud Platform (GCP).

Při připojování účtu AWS do Security Center se integruje Centrum zabezpečení AWS a Azure Security Center. Security Center proto zajišťuje viditelnost a ochranu v obou těchto cloudových prostředích, aby poskytovala tyto informace:

- Automatické zřizování agentů (Security Center využívá [Azure ARC](../azure-arc/servers/overview.md) k nasazení agenta Log Analytics do instancí AWS)
- Správa zásad
- Správa ohrožení zabezpečení
- Rozpoznání a odpověď vloženého koncového bodu (EDR)
- Zjišťování nezabezpečených konfigurací zabezpečení
- Jediné zobrazení, které ukazuje Security Center doporučení a AWS centra zabezpečení
- Začlenění prostředků AWS do výpočtů zabezpečeného skóre Security Center
- Posouzení dodržování předpisů u vašich AWS prostředků v legislativě

Na snímku obrazovky níže vidíte účty AWS zobrazené na řídicím panelu přehled Security Center.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="3 projekty GCP uvedené na řídicím panelu přehled Security Center" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Preview|
|Stanov|Vyžaduje [Azure Defender pro servery](defender-for-servers-introduction.md) .|
|Požadované role a oprávnění:|**Vlastník** nebo **Přispěvatel** v příslušném předplatném Azure|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||



## <a name="connect-your-aws-account"></a>Připojení účtu AWS

### <a name="step-1-set-up-aws-security-hub"></a>Krok 1. Nastavení centra zabezpečení AWS:

1. Chcete-li zobrazit doporučení zabezpečení pro více oblastí, opakujte následující kroky pro všechny příslušné oblasti.

    > [!IMPORTANT]
    > Pokud používáte hlavní účet AWS, opakujte následující tři kroky, abyste nakonfigurovali hlavní účet a všechny připojené členské účty ve všech příslušných oblastech.

    1. Povolte [AWS config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Povolte [Centrum zabezpečení AWS](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Ověřte, že se data nanášejí do centra zabezpečení.

        Při prvním povolení centra zabezpečení může trvat několik hodin, než budou data k dispozici.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>Krok 2. Nastavení ověřování pro Security Center v AWS

Existují dva způsoby, jak Security Center ověřit pro AWS:

- **Vytvoření role IAM pro Security Center** – jedná se o nejbezpečnější metodu, která se doporučuje.
- **AWS uživatel pro Security Center** -a méně bezpečnou možnost, pokud nemáte POVOLENý IAM

#### <a name="create-an-iam-role-for-security-center"></a>Vytvoření role IAM pro Security Center
1. V konzole Amazon Web Services v části **zabezpečení, identita & kompatibilita**vyberte **IAM**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="Služby AWS":::

1. Vyberte **role** a **vytvořit roli**.
1. Vyberte **jiný účet AWS**.
1. Zadejte následující podrobnosti:

    - **ID účtu** – zadejte ID účtu Microsoft (**158177204117**), jak je znázorněno na stránce konektoru AWS v Security Center.
    - **Vyžadovat externí ID** – by se mělo vybrat
    - **Externí ID** – zadejte ID předplatného, jak je znázorněno na stránce konektoru AWS v Security Center 

1. Vyberte **Další**.
1. V části **připojit zásady oprávnění** vyberte následující zásady:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Volitelně můžete přidat značky. Přidání značek uživateli nemá vliv na připojení.
1. Vyberte **Další**.

1. V seznamu role vyberte roli, kterou jste vytvořili.

1. Uložte název prostředku Amazon (ARN) pro pozdější verzi. 

#### <a name="create-an-aws-user-for-security-center"></a>Vytvoření uživatele AWS pro Security Center 
1. Otevřete kartu **Uživatelé** a vyberte **Přidat uživatele**.
1. V kroku **podrobností** zadejte uživatelské jméno pro Security Center a ujistěte se, že jste pro typ přístupu AWS vybrali možnost **programový přístup** . 
1. Vyberte **Další oprávnění**.
1. Vyberte možnost **připojit existující zásady přímo** a použijte následující zásady:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Vyberte **Další: značky**. Volitelně můžete přidat značky. Přidání značek uživateli nemá vliv na připojení.
1. Vyberte **zkontrolovat**.
1. Uložte automaticky vygenerované **ID přístupového klíče** a soubor CSV **tajného přístupového** klíče pro pozdější verzi.
1. Zkontrolujte souhrn a klikněte na **vytvořit uživatele**.


### <a name="step-3-configure-the-ssm-agent"></a>Krok 3. Konfigurace agenta SSM

AWS Systems Manager se vyžaduje pro automatizaci úloh napříč prostředky AWS. Pokud vaše instance EC2 nemají agenta SSM, postupujte podle příslušných pokynů z Amazon:

- [Instalace a konfigurace agenta SSM v instancích systému Windows](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Instalace a konfigurace agenta SSM na instancích Amazon EC2 Linux](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-create-a-service-principal-for-onboarding-at-scale"></a>Krok 4: Vytvoření instančního objektu pro registraci ve velkém měřítku

Jako **vlastník** předplatného, který chcete použít pro registraci, vytvořte instanční objekt pro Azure ARC, jak je popsáno v tématu [Vytvoření instančního objektu pro registraci ve velkém měřítku](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) .


### <a name="step-5-connect-aws-to-security-center"></a>Krok 5. Připojení AWS k Security Center

1. V nabídce Security Center vyberte **multi cloudové konektory**.
1. Vyberte **Přidat účet AWS**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Tlačítko Přidat účet AWS na stránce s více síťovými konektory v Security Center":::
1. Nakonfigurujte možnosti na kartě **ověřování AWS** :
    1. Zadejte **Zobrazovaný název** pro konektor.
    1. Potvrďte, že je předplatné správné. Je to předplatné, které bude zahrnovat doporučení centra zabezpečení konektoru a AWS.
    1. V závislosti na možnosti ověřování, kterou jste zvolili v [kroku 2. Nastavení ověřování pro Security Center v AWS](#step-2-set-up-authentication-for-security-center-in-aws):
        - Vyberte **předpokládat roli** a vložte ARN z části [Vytvoření Role IAM pro Security Center](#create-an-iam-role-for-security-center) :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="vkládání souboru ARN do příslušného pole Průvodce připojením AWS na webu Azure Portal"::: .

            NEBO

        - Vyberte **přihlašovací údaje** a vložte **přístupový klíč** a **tajný klíč** ze souboru. csv, který jste uložili v části [Vytvoření uživatele AWS pro Security Center](#create-an-aws-user-for-security-center).
1. Vyberte **Další**.
1. Nakonfigurujte možnosti na kartě **konfigurace ARC Azure** :

    Security Center zjistí instance EC2 v připojeném účtu AWS a pomocí SSM je zaregistruje do Azure ARC. 

    > [!TIP]
    > Seznam podporovaných operačních systémů najdete v části Nejčastější dotazy.

    1. Vyberte **skupinu prostředků** a **oblast Azure** , ke které se zjištěné AWS EC2s připojí do vybraného předplatného.
    1. Zadejte **ID instančního** objektu a **tajný klíč klienta instančního objektu** pro Azure ARC, jak je popsané tady. [Vytvoření instančního objektu pro registraci ve velkém měřítku](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Pokud se počítač připojuje k Internetu prostřednictvím proxy server, zadejte IP adresu proxy server nebo název a číslo portu, které počítač používá ke komunikaci s proxy server. Zadejte hodnotu ve formátu. ```http://<proxyURL>:<proxyport>```
    1. Vyberte **Zkontrolovat a vytvořit**.

        Kontrola souhrnných informací

        V sekcích značek se zobrazí seznam všech značek Azure, které se automaticky vytvoří pro každý EC2, s vlastními příslušnými podrobnostmi, které ji snadno rozpoznají v Azure. 

        Další informace o značkách Azure v [používání značek k uspořádání prostředků Azure a hierarchie správy](../azure-resource-manager/management/tag-resources.md).

### <a name="step-7-confirmation"></a>Krok 7. Confirmation (Potvrzení)

Po úspěšném vytvoření konektoru a správné konfiguraci centra zabezpečení AWS:

- Security Center prohledá prostředí pro instance AWS EC2, zaregistruje je do Azure ARC, umožní nainstalovat agenta Log Analytics a zajistit ochranu před hrozbami a bezpečnostní doporučení. 
- Služba ASC vyhledává nové instance EC2 AWS každých 6 hodin a zaregistruje je podle konfigurace.
- Standard AWS CIS se zobrazí v řídicím panelu dodržování předpisů pro Security Center.
- Pokud je zapnutá zásada centra zabezpečení, na portálu Security Center a řídicím panelu dodržování předpisů se zobrazí doporučení 5-10 minut po dokončení zprovoznění.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="AWS prostředky a doporučení na stránce s doporučeními pro Security Center":::



## <a name="monitoring-your-aws-resources"></a>Monitorování prostředků AWS

Jak vidíte výše, stránka doporučení pro zabezpečení Azure Security Center zobrazuje vaše prostředky AWS společně s prostředky Azure a GCP pro opravdové zobrazení s více cloudy.

Pokud chcete zobrazit všechna aktivní doporučení pro vaše prostředky podle typu prostředku, použijte stránku inventáře assetů Security Center a filtrujte na typ prostředku AWS, ve kterém máte zájem:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Filtr typu prostředku stránky inventáře prostředků zobrazující možnosti AWS"::: 


## <a name="aws-in-security-center-faq"></a>AWS v nejčastějších dotazech k Security Center

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Jaké operační systémy pro instance EC2 jsou podporované?

Podporovaný operační systém pro automatické připojování do Azure ARC pro počítače s AWS

- Ubuntu 16,04 – agent SSM je předinstalovaný, ve výchozím nastavení.
- Ubuntu 18,04 – agent SSM je předinstalovaný, ve výchozím nastavení.
- Windows Server – Agent SSM je ve výchozím nastavení předem nainstalovaný.
- CentOS Linux 7 – SSM by se měly nainstalovat ručně nebo připojit samostatně.
- SUSE Linux Enterprise Server (SLES) 15 (x64) – SSM by se měla nainstalovat ručně nebo samostatně připojit.
- Red Hat Enterprise Linux (RHEL) 7 (x64) – SSM by se mělo nainstalovat ručně nebo samostatně připojit


## <a name="next-steps"></a>Další kroky

Připojení účtu AWS je součástí prostředí s více cloudy, které je dostupné v Azure Security Center. Související informace najdete na následující stránce:

- [Připojení účtů GCP k Azure Security Center](quickstart-onboard-gcp.md)
