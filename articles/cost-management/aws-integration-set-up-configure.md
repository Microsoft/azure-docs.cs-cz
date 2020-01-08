---
title: Nastavení integrace AWS s Azure Cost Management
description: Tento článek vás provede nastavením a konfigurací integrace AWS nákladů a sestav využití pomocí Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 77bc7772e1d9b8144199c865a8d82f818d5e1963
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444598"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Nastavení a konfigurace integrace sestav AWS a nákladů na využití

Díky integraci Amazon Web Services (AWS) a sestavy využití (stejné) můžete monitorovat a řídit své AWS útraty v Azure Cost Management. Integrace umožňuje jedno umístění v Azure Portal, kde můžete monitorovat a řídit útratu pro Azure i pro AWS. Tento článek vysvětluje, jak nastavit integraci a nakonfigurovat ji, aby bylo možné používat funkce Azure Cost Management k analýze nákladů a kontrole rozpočtů.

Cost Management zpracovává sestavu nákladů a využití AWS uložených v intervalu S3 pomocí přihlašovacích údajů AWS pro přístup k získání definic sestav a stažení sestav souborů CSV protokolu GZIP.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Vytvoření sestavy nákladů a využití v AWS

Použití sestavy náklady a využití je AWS způsobem doporučeným pro shromažďování a zpracování AWS nákladů. Další informace najdete v dokumentaci [AWS Cost and Usage Report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) .

Na stránce **sestavy využití & nákladů** a cost management v konzole AWS můžete vytvořit sestavu nákladů a využití pomocí následujících kroků:

1. Přihlaste se ke konzole pro správu AWS a otevřete [konzolu pro fakturaci a cost management](https://console.aws.amazon.com/billing).
2. V navigačním podokně vyberte **náklady & sestavy využití**.
3. Vyberte **vytvořit sestavu**.
4. Jako **název sestavy**zadejte název sestavy.
5. V části **Další podrobnosti sestavy**vyberte **zahrnout ID prostředků**.
6. V části **Nastavení aktualizace dat**vyberte, jestli chcete, aby se sestava AWS náklady a využití aktualizovala v případě, že AWS po dokončení faktury aplikuje refundace, kredity nebo poplatky za podporu k vašemu účtu. Při aktualizaci sestavy se do Amazon S3 nahraje Nová sestava. Doporučujeme ponechat nastavení vybrané.
7. Vyberte **Next** (Další).
8. V případě **intervalu S3**klikněte na tlačítko **Konfigurovat**.
9. V dialogovém okně Konfigurovat interval S3 proveďte jednu z následujících úloh:
    1. V rozevíracím seznamu vyberte existující kontejner a klikněte na tlačítko **Další**.
    2. Zadejte název a oblast, kde chcete vytvořit nový kontejner, a klikněte na tlačítko **Další**.
10. Vyberte možnost **potvrzuji, že je tato zásada správná**, a pak klikněte na **Uložit**.
11. Volitelné V poli Předpona cesty k sestavě zadejte předponu cesty k sestavě, kterou chcete přidat k názvu vaší sestavy.
Pokud nezadáte předponu, výchozí předpona je název, který jste zadali pro sestavu. Rozsah kalendářních dat má formát `/report-name/date-range/`.
12. V případě **časové jednotky**vyberte **každou hodinu**.
13. Pro **správu verzí sestav**vyberte, zda chcete, aby každá verze sestavy přepsala předchozí verzi, nebo pokud chcete další nové sestavy.
14. Pro **možnost povolit integraci dat pro**není žádný výběr nutný.
15. V případě **Komprese**vyberte **gzip**.
16. Vyberte **Next** (Další).
17. Po kontrole nastavení sestavy vyberte **zkontrolovat a dokončit**.

    Poznamenejte si název sestavy. Použijete ho v pozdějších krocích.

AWS může trvat až 24 hodin, než se zahájí doručování sestav do vašeho intervalu Amazon S3. Po zahájení doručování AWS aktualizuje soubory sestav náklady AWS a využití alespoň jednou denně. Můžete pokračovat v konfiguraci prostředí AWS, aniž byste čekali na zahájení doručování.

## <a name="create-a-role-and-policy-in-aws"></a>Vytvoření role a zásad v AWS

Azure Cost Management přistupuje k bloku S3, kde se sestava náklady a využití nacházela několikrát denně. Služba potřebuje přístup k přihlašovacím údajům pro kontrolu nových dat. V AWSu vytvoříte roli a zásadu, která umožní přístup k ní Cost Management.

Pokud chcete povolit přístup na základě role k účtu AWS v Cost Management, role se vytvoří v konzole AWS. Musíte mít ARN a _externí ID_ _role_ z konzoly AWS. Později je můžete použít na stránce **konektoru vytvořit AWS** v cost management.

Použijte Průvodce vytvořením nové role:

1. Přihlaste se ke konzole AWS a vyberte **služby**.
2. V seznamu služeb vyberte **IAM**.
3. Vyberte **role** a pak vyberte **vytvořit roli**.
4. Na další stránce vyberte **jiný účet AWS**.
5. Do **ID účtu**zadejte **432263259397**.
6. V **Možnosti**vyberte **vyžadovat externí ID (osvědčené postupy, pokud bude tato role platit třetí strana)** .
7. Do pole **externí ID**zadejte externí ID, které je sdílené heslo mezi rolí AWS a Azure cost management. Stejné externí ID se používá také na stránce **nový konektor** v cost management. Microsoft doporučuje, abyste při zadávání externího ID použili zásady silného hesla.

    > [!NOTE]
    > Neměňte výběr pro **vyžadovat MFA**. Měla by zůstat nezaškrtnutá.
8. Vyberte **Další: oprávnění**.
9. Vyberte **vytvořit zásadu**. Otevře se nová karta prohlížeče. V takovém případě vytvoříte zásadu.
10. Vyberte **možnost zvolit službu**.

Konfigurovat oprávnění pro sestavu náklady a využití:

1. Zadejte **náklady a sestavu využití**.
2. Vyberte **úroveň přístupu** > **číst** > **DescribeReportDefinitions**. Tento krok umožňuje Cost Management přečíst stejné sestavy, které jsou definovány, a určit, jestli se shodují se splněním požadavků definice sestavy.
3. Vyberte **Přidat další oprávnění**.

Konfigurovat oprávnění pro vaše bloky a objekty S3:

1. Vyberte **možnost zvolit službu**.
2. Zadejte **S3**.
3. Vyberte **úroveň přístupu** > **seznamu** > **ListBucket**. Tato akce načte seznam objektů v intervalu S3.
4. Vyberte **úroveň přístupu** > **číst** > **GetObject**. Tato akce umožňuje stažení fakturačních souborů.
5. Vyberte **prostředky**.
6. Vyberte možnost **kontejner – přidat ARN**.
7. Do pole **název**sady zadejte interval, ve kterém se mají ukládat stejné soubory.
8. Vyberte **objekt – přidat ARN**.
9. Do pole **název**sady zadejte interval, ve kterém se mají ukládat stejné soubory.
10. V **názvu objektu**vyberte **libovolný**.
11. Vyberte **Přidat další oprávnění**.

Konfigurovat oprávnění pro Průzkumníka nákladů:

1. Vyberte **možnost zvolit službu**.
2. Zadejte **službu cost Explorer**.
3. Vyberte **všechny akce služby cost Explorer (CE:\*)** . Tato akce ověří, zda je kolekce správná.
4. Vyberte **Přidat další oprávnění**.

Přidání oprávnění pro organizace AWS:

1. Zadejte **organizace**.
2. Vyberte **úroveň přístupu** > **seznamu** > **ListAccounts**. Tato akce Získá názvy účtů.
3. V části **zkontrolovat zásadu**zadejte název nové zásady. Zkontrolujte, zda jste zadali správné informace, a pak vyberte **vytvořit zásadu**.
4. Vraťte se na předchozí kartu a aktualizujte webovou stránku prohlížeče. Na panelu hledání vyhledejte novou zásadu.
5. Vyberte **Další: zkontrolovat**.
6. Zadejte název nové role. Zkontrolujte, zda jste zadali správné informace, a pak vyberte **vytvořit roli**.

    Všimněte si, že role ARN a externí ID použité v předchozích krocích při vytváření role. Později je budete používat při nastavování konektoru Azure Cost Management.

JSON zásad by měl vypadat podobně jako v následujícím příkladu. Parametr _interval_ nahraďte názvem svého intervalu S3.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Nastavení nového konektoru AWS v Azure

Pomocí následujících informací vytvořte konektor AWS a začněte monitorovat náklady na AWS:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Přejít na **cost management a fakturační** > **cost management**.
3. V části **Nastavení**vyberte **cloudové konektory (Preview)** .  
    ![Příklad znázorňující nastavení cloudové konektory (Preview)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. V horní části stránky vyberte **+ Přidat** a vytvořte konektor.
5. Na stránce **Vytvoření konektoru AWS** zadejte do pole **Zobrazovaný název**název vašeho konektoru.  
    ![příklad stránky pro vytvoření konektoru AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Volitelně můžete vybrat výchozí skupinu pro správu. Budou se ukládat všechny zjištěné propojené účty. Můžete ho nastavit později.
7. V části **fakturace** vyberte **automaticky účtovat 1% při obecné dostupnosti** , pokud chcete zajistit nepřetržitou operaci, když platnost vyprší. Pokud vyberete možnost automaticky, musíte vybrat fakturační předplatné.
8. Do pole **role ARN**zadejte hodnotu, kterou jste použili při nastavení role v AWS.
9. Pro **externí ID**zadejte hodnotu, kterou jste použili při nastavení role v AWS.
10. Jako **název sestavy**zadejte název, který jste vytvořili v AWS.
11. Vyberte **Další** a pak vyberte **vytvořit**.

Může to trvat několik hodin, než se objeví nové obory AWS, AWS konsolidovaný účet, AWS propojené účty a jejich nákladová data.

Po vytvoření konektoru doporučujeme, abyste k němu přiřadili řízení přístupu. Uživatelům jsou přiřazena oprávnění k nově zjištěným oborům: AWS konsolidovaný účet a AWS propojené účty. Uživatel, který vytváří konektor, je vlastníkem konektoru, konsolidovaného účtu a všech propojených účtů.

Přiřazení oprávnění k konektoru uživatelům po zjištění, že dojde ke zjišťování, nepřiřazuje oprávnění existujícím oborům AWS. Místo toho jsou přiřazena oprávnění pouze novým propojeným účtům.

## <a name="take-additional-steps"></a>Provedení dalších kroků

- [Nastavte skupiny pro správu](../governance/management-groups/overview.md#initial-setup-of-management-groups), pokud jste to ještě neudělali.
- Ověřte, že se do výběru oboru přidaly nové obory. Kliknutím na **aktualizovat** zobrazíte nejnovější data.
- Na stránce **cloudové konektory** vyberte svůj konektor a vyberte **Přejít k fakturačnímu účtu** , abyste přiřadili propojený účet ke skupinám pro správu.

## <a name="manage-cloud-connectors"></a>Správa cloudových konektorů

Když vyberete konektor na stránce **cloudové konektory** , můžete:

- Výběrem možnosti **Přejít na fakturační účet** zobrazíte informace pro konsolidovaný účet AWS.
- Vyberte **Access Control** pro správu přiřazení role pro konektor.
- Vyberte **Upravit** pro aktualizaci konektoru. Číslo účtu AWS nemůžete změnit, protože se zobrazuje v ARN role. Můžete ale vytvořit nový konektor.
- Vyberte možnost **ověřit** a znovu spusťte ověřovací test, abyste se ujistili, že cost management mohou shromažďovat data pomocí nastavení konektoru.

![Ukázkový seznam vytvořených konektorů AWS](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Nastavení skupin pro správu Azure

Přiložte své předplatné Azure a propojené účty AWS ve stejné skupině pro správu, abyste vytvořili jedno místo, kde vidíte informace o poskytovateli mezi cloudy. Pokud jste prostředí Azure ještě nenakonfigurovali pomocí skupin pro správu, přečtěte si téma [počáteční nastavení skupin pro správu](../governance/management-groups/overview.md#initial-setup-of-management-groups).

Pokud chcete rozdělit náklady, můžete vytvořit skupinu pro správu, která obsahuje jenom AWS propojené účty.

## <a name="set-up-an-aws-consolidated-account"></a>Nastavení konsolidovaného účtu AWS

Konsolidovaný účet AWS kombinuje fakturaci a platbu za několik účtů AWS. Funguje taky jako propojený účet AWS.

![Příklady podrobností pro konsolidovanou AWS účet](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Na stránce můžete:

- Pokud chcete hromadně aktualizovat přidružení účtů AWS ke skupině pro správu, vyberte **aktualizovat** .
- Vyberte **Access Control** pro nastavení přiřazení role pro obor.

### <a name="permissions-for-an-aws-consolidated-account"></a>Oprávnění pro konsolidovanou AWS účet

Ve výchozím nastavení jsou oprávnění pro konsolidovaný účet AWS nastavena na vytvoření účtu na základě oprávnění konektoru AWS. Tvůrce konektoru je vlastníkem.

Úroveň přístupu můžete spravovat pomocí stránky **úroveň přístupu** konsolidovaného účtu AWS. Propojené účty AWS ale nedědí oprávnění k konsolidovanému účtu AWS.

## <a name="set-up-an-aws-linked-account"></a>Nastavení propojeného účtu AWS

Propojený účet AWS je místo, kde se vytvářejí a spravují prostředky AWS. Propojený účet funguje také jako hranice zabezpečení.

Na této stránce můžete:

- Vyberte **aktualizovat** , pokud chcete aktualizovat přidružení propojeného účtu AWS ke skupině pro správu.
- Vyberte **Access Control** pro nastavení přiřazení role pro obor.

![Příklad stránky propojeného účtu AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Oprávnění pro propojený účet AWS

Ve výchozím nastavení jsou oprávnění pro propojený účet AWS nastavena při vytváření na základě oprávnění konektoru AWS. Tvůrce konektoru je vlastníkem. Úroveň přístupu můžete spravovat pomocí stránky **úroveň přístupu** propojeného účtu AWS. Propojené účty AWS nedědí oprávnění z konsolidovaného účtu AWS.

Propojené účty AWS vždy dědí oprávnění ze skupiny pro správu, do které patří.

## <a name="next-steps"></a>Další kroky

- Teď, když jste nastavili a nakonfigurovali integraci sestav AWS a nákladů na používání, budete moct dál [Spravovat AWS náklady a využití](aws-integration-manage.md).
- Pokud nejste obeznámeni s analýzou nákladů, přečtěte si téma [prozkoumat a analyzovat náklady pomocí rychlého startu analýzy nákladů](quick-acm-cost-analysis.md) .
- Pokud si nejste zvyklí rozpočty v Azure, přečtěte si téma [Vytvoření a Správa rozpočtů Azure](tutorial-acm-create-budgets.md).
