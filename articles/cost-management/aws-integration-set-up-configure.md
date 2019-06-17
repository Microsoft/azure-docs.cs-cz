---
title: Nastavení a konfigurace AWS nákladů a využití integrace sestavy ve službě Azure Cost Management
description: Tento článek vás provede nastavením a konfigurace AWS nákladů a využití integrace sestavy ve službě Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002126"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Nastavení a konfigurace AWS nákladů a využití integrace sestavy

Amazon Web Services (AWS) nákladů a využití integrace sestavy (MĚNA), můžete monitorovat a kontrolovat útraty AWS ve službě Azure Cost Management. Integrace umožňuje na jednom místě na webu Azure portal, kde můžete monitorovat a ovládací prvek výdaje za Azure a AWS. Tento článek vysvětluje, jak nastavit integraci a nakonfigurujte ho tak, aby analýza nákladů a zkontrolujte rozpočty pomocí funkce Azure Cost Management.

Z hlediska nákladů procesů správy AWS nákladů a využití sestavy, které jsou uložené v sady S3 pomocí vašich přihlašovacích údajů AWS přístup k získání definice sestav a stáhnout sestavu, kterou souborů GZIP sdíleného svazku clusteru.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Vytvoření sestavy nákladů a využití v AWS

Pomocí sestavy nákladů a využití je doporučeno AWS způsob, jak shromažďovat a zpracovávat náklady na AWS. Další informace najdete v tématu [náklady na AWS a sestava využití](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) dokumentaci.

Použití **nákladů a sestav využití** stránka konzoly správou nákladů a fakturací v AWS, chcete-li vytvořit sestavy nákladů a využití pomocí následujících kroků:

1. Přihlášení do konzoly pro správu AWS a otevřete [správou nákladů a fakturací konzoly](https://console.aws.amazon.com/billing).
2. V navigačním podokně vyberte **nákladů a sestav využití**.
3. Vyberte **vytvoření sestavy**.
4. Pro **název sestavy**, zadejte název pro sestavu.
5. Pro **další sestava obsahuje podrobnosti o** , aby zahrnoval ID každého prostředku sestavy a vyberte **zahrnují ID prostředků**.
6. Pro **nastavení aktualizace dat**, vyberte, jestli chcete aktualizovat, pokud platí AWS finanční prostředky, kredity, sestava náklady na AWS a využití nebo podporu po dokončení vyúčtování poplatky ke svému účtu. Při aktualizaci sestavy do Amazon S3 nahraje nové sestavy. Doporučuje se toto nastavení nechávat na.
7. Vyberte **Další**.
8. Pro **sady S3**, zvolte **konfigurovat**.
9. V dialogovém okně Konfigurace sady S3 proveďte jednu z následujících akcí:
    1. Z rozevíracího seznamu vyberte existující sady a zvolte **Další**.
    2. Zadejte název kontejneru a oblasti, ve kterém chcete vytvořit nový blok a zvolte **Další**.
10. Vyberte, které můžu ujistíte, že tato zásada je opravit a vyberte Uložit.
11. (Volitelné) Předpona cesty sestavu zadejte předponu cesta sestavy, která má prepended na název sestavy.
Pokud nezadáte předponu, výchozí předponou je název, který jste zadali pro sestavu v kroku 4 a rozsah dat pro sestavy, v následujícím formátu: `/report-name/date-range/`
12. Pro **časovou jednotku**, zvolte **hodinové**.
13. Pro **sestav správy verzí**, vyberte, zda má každá verze sestavy přepsat předchozí verze sestavy nebo který bude doručen kromě předchozích verzí.
14. Pro **povolit integraci modulu dat pro**, není nutný žádný výběr.
15. Pro **komprese**vyberte **GZIP**.
16. Vyberte **Další**.
17. Po zkontrolování nastavení sestavy, vyberte **zkontrolujte a dokončete**.

    Poznamenejte si název sestavy. Použijete ho v dalších krocích.

Může trvat až 24 hodin AWS ke spuštění doručování sestavy do vašeho kontejneru Amazon S3. Po spuštění doručování AWS aktualizuje soubory sestav AWS nákladů a využití alespoň jednou za den. Můžete pokračovat v konfiguraci vašeho prostředí AWS bez čekání na doručování na spuštění.

## <a name="create-a-role-and-policy-in-aws"></a>Vytvoření role a zásad AWS

Azure Cost Management zajišťuje přístup ke kontejneru S3, kde nákladů a využití je sestava umístěna několikrát za den. Služba potřebuje přístup k přihlašovacím údajům a kontrolují dostupnost nových dat. Vytvoření role a zásad v AWS umožňující Cost Management pro přístup k ní.

Pokud chcete povolit přístup na základě rolí k účtu AWS ve službě Cost Management, je role vytvořené v konzole AWS. Je potřeba mít _role ARN_ a _externí ID_ v konzole AWS. Později, je použít na **vytvoření konektoru služby AWS** stránky ve službě Cost Management.

Pomocí Průvodce vytvořením nové Role:

1. Přihlaste se ke konzole AWS a vyberte **služby**.
2. V seznamu služeb vyberte **IAM**.
3. Vyberte **role** a pak vyberte **vytvořit roli**.
4. Na další stránce vyberte **účtu AWS jiného**.
5. V **ID účtu**, zadejte **432263259397**.
6. V **možnosti**vyberte **vyžadují externí ID (doporučený postup při externí bude předpokládat této role)** .
7. V **externí ID**, zadejte externího ID. Externí ID se sdílený přístupový kód mezi AWS role a Azure Cost Management. Stejný externí ID se také používá na **nový konektor** stránky ve službě Cost Management. Například vypadá podobně jako externí ID _Companyname1234567890123_.

    > [!NOTE]
    > Neměnit výběr **vyžadovat vícefaktorové ověřování**. By měla zůstat prázdné.
8. Vyberte **Další: Oprávnění**.
9. Vyberte **vytvořit zásadu**. Otevře se nová karta prohlížeče. To je, kde vytvoříte zásady.
10. Vyberte **vybrat službu**.

Konfigurace oprávnění pro sestavy nákladů a využití:

1. Zadejte **náklady a sestava využití**.
2. Vyberte **úroveň přístupu** > **čtení** > **DescribeReportDefinitions**. Tento krok umožňuje Cost Management, přečtěte si, jaké stejné sestavy jsou definovány a určit, pokud se shodují kontrolu požadovaných součástí definice sestavy.
3. Vyberte **přidat další oprávnění**.

Konfigurace oprávnění pro sadu S3 a objekty:

1. Vyberte **vybrat službu**.
2. Zadejte **S3**.
3. Vyberte **úroveň přístupu** > **seznamu** > **ListBucket**. Tato akce načte seznam objektů v intervalu S3.
4. Vyberte **úroveň přístupu** > **čtení** > **GetObject**. Tato akce umožňuje stáhnout soubory fakturace.
5. Vyberte **prostředky**.
6. Vyberte **kontejneru – přidání ARN**.
7. V **název sektoru**, zadejte do kbelíku používá k ukládání souborů MĚNA.
8. Vyberte **objekt – přidání ARN**.
9. V **název sektoru**, zadejte do kbelíku používá k ukládání souborů MĚNA.
10. V **název objektu**vyberte **jakékoli**.
11. Vyberte **přidat další oprávnění**.

Konfigurace oprávnění pro Průzkumníka náklady:

1. Vyberte **vybrat službu**.
2. Zadejte **nákladů Průzkumníka služby**.
3. Vyberte **akce Explorer – všechny náklady na služby (ce:\*)** . Tato akce ověří správnost kolekce.
4. Vyberte **přidat další oprávnění**.

Přidání oprávnění pro AWS organizace:

1. Zadejte **organizace**.
2. Vyberte **úroveň přístupu** > **seznamu** > **Vypisovat účty**. Tato akce získá názvy účtů.
3. V **zásady revize**, zadejte název pro novou zásadu. Zkontrolujte, zda jste zadali správné informace a pak vyberte **vytvořit zásadu**.
4. Přejděte zpět na předchozí kartu a aktualizujte webovou stránku v prohlížeči. Na panelu hledání vyhledejte novou zásadu.
5. Vyberte **Další: Revize**.
6. Zadejte název pro novou roli. Zkontrolujte, zda jste zadali správné informace a pak vyberte **vytvořit roli**.

    Poznámka: tuto roli ARN a externí ID v předchozích krocích jste použili při vytváření role. Použijete je později při nastavování konektoru Azure Cost Management.

Zásady JSON by měl vypadat podobně jako v následujícím příkladu. Nahraďte _bucketname_ s názvem vaší sady S3.

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

## <a name="set-up-a-new-aws-connector-in-azure"></a>Vytvořit nový konektor AWS v Azure

Vytvoření konektoru služby AWS a začít monitorovat vaše náklady na AWS, použijte následující informace:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte na **Cost Management a fakturace** > **Cost Management**.
3. V části **nastavení**vyberte **konektory (Preview) v cloudu**.  
    ![Příklad zobrazující cloudu konektory (Preview) nastavení)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Vyberte **+ přidat** v horní části stránky vytvořte konektor.
5. Na **vytvoření konektoru služby AWS** stránce **zobrazovaný název**, zadejte název pro váš konektor.  
    ![Příklad stránky pro vytvoření konektoru služby AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Volitelně vyberte výchozí skupiny pro správu. Uloží všechny zjištěné propojené účty. Můžete nastavit ji později.
7. V **fakturace** vyberte **automaticky účtovat 1 % ve fázi obecné dostupnosti** Pokud chcete zajistit nepřetržité provádění po vypršení platnosti verze preview. Pokud vyberete automatickou možnost, musíte vybrat předplatné pro fakturaci.
8. Pro **Role ARN**, zadejte hodnotu, kterou jste použili při nastavování role v AWS.
9. Pro **externí ID**, zadejte hodnotu, kterou jste použili při nastavování role v AWS.
10. Pro **název sestavy**, zadejte název, který jste vytvořili v AWS.
11. Vyberte **Další** a pak vyberte **vytvořit**.

Může trvat několik hodin pro nové obory AWS, konsolidovat AWS, účet a AWS, propojené účty a jejich nákladech se zobrazí.

Když vytvoříte konektor, doporučujeme přiřadit řízení přístupu k němu. Uživatelé jsou přiřazení oprávnění pro nově zjištěné obory: AWS konsolidovat účet a AWS, propojené účty. Uživatel, který vytvoří konektor je vlastníkem konektoru, konsolidované účet a všechny propojené účty.

Přiřazení oprávnění konektorů uživatelům, když dojde k zjišťování nebude přiřadit oprávnění ke stávající obory AWS. Místo toho jsou pouze nové propojené účty přiřadit oprávnění.

## <a name="take-additional-steps"></a>Další kroky

- [Nastavení skupin pro správu](../governance/management-groups/index.md#initial-setup-of-management-groups), pokud jste tak již neučinili.
- Zkontrolujte, že nové obory se přidají do vašeho výběr oboru. Vyberte **aktualizovat** zobrazíte nejnovější data.
- Na **cloudu konektory** stránce vyberte váš konektor a vyberte **přejít na fakturační účet** propojeného účtu přiřadit ke skupinám pro správu.

## <a name="manage-cloud-connectors"></a>Správa cloudových konektorů

Když vyberete konektor na **cloudu konektory** stránky, můžete:

- Vyberte **přejděte do fakturačního účtu** zobrazit informace o AWS konsolidovaných účtu.
- Vyberte **řízení přístupu** Spravovat přiřazení rolí konektoru.
- Vyberte **upravit** k aktualizaci konektoru. Číslo účtu AWS, nelze změnit, protože se zobrazí v roli ARN. Ale můžete vytvořit nový konektor.
- Vyberte **ověřte** znovu spustit ověřovací test, abyste měli jistotu, že Cost Management může shromažďovat data s použitím nastavení konektoru.

![Seznam příkladů vytvořených konektorů AWS](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Nastavení skupin pro správu Azure

Chcete-li vytvořit na jednom místě, chcete-li zobrazit informace o poskytovateli cloudu, musíte umístit vaše předplatná Azure a AWS, propojené účty ve stejné skupině pro správu. Pokud už jste nenakonfigurovali prostředí Azure pomocí skupin pro správu, přečtěte si téma [počáteční nastavení skupin pro správu](../governance/management-groups/index.md#initial-setup-of-management-groups).

Pokud chcete oddělit náklady, můžete vytvořit skupinu pro správu, který obsahuje pouze AWS, propojené účty.

## <a name="set-up-an-aws-consolidated-account"></a>Nastavení AWS konsolidovat účtu

Účtu AWS konsolidovat kombinuje fakturace a plateb pro několik účtů AWS. Slouží také jako AWS propojený účet.

![Podrobný příklad AWS konsolidovat účtu](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Na stránce můžete:

- Vyberte **aktualizovat** hromadné aktualizace přidružení AWS propojené účty se skupinou pro správu.
- Vyberte **řízení přístupu** k nastavení oboru přiřazení role.

### <a name="permissions-for-an-aws-consolidated-account"></a>Oprávnění pro AWS konsolidovat účtu

Ve výchozím nastavení jsou oprávnění účtu AWS konsolidovat nastavena při vytvoření účtu, na základě oprávnění konektoru AWS. Tvůrce konektoru je vlastníkem.

Spravovat úrovně přístupu pomocí **úroveň přístupu** stránky AWS konsolidovat účtu. Ale AWS propojené účty Nedědit oprávnění k účtu AWS konsolidovat.

## <a name="set-up-an-aws-linked-account"></a>Nastavit účet propojený AWS

AWS propojený účet je, kde se vytváří a spravují prostředky AWS. Propojený účet také slouží jako hranice zabezpečení.

Na této stránce můžete:

- Vyberte **aktualizovat** aktualizovat přidružení AWS propojený účet s skupinu pro správu.
- Vyberte **řízení přístupu** k nastavení oboru přiřazení role.

![Příklad stránky propojeného účtu AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Propojený účet oprávnění pro AWS

Ve výchozím nastavení jsou oprávnění účtu AWS propojené nastavena při vytvoření, na základě oprávnění konektoru AWS. Tvůrce konektoru je vlastníkem. Spravovat úrovně přístupu pomocí **úroveň přístupu** stránky AWS propojený účet. AWS, propojené účty nejsou dědit oprávnění z účtu AWS konsolidovat.

Propojené účty AWS vždy dědí oprávnění ze skupiny pro správu, do které patří.

## <a name="next-steps"></a>Další postup

- Teď, když jste nastavili a nakonfigurovali AWS nákladů a využití integrace sestavy, přejít k [AWS Správa nákladů a využití](aws-integration-manage.md).
- Pokud nejste obeznámeni s analýzy nákladů, přečtěte si téma [zkoumat a analyzovat náklady pomocí cost analysis](quick-acm-cost-analysis.md) rychlý start.
- Pokud nejste obeznámeni s rozpočty v Azure, přečtěte si téma [vytvořit a spravovat Azure rozpočty](tutorial-acm-create-budgets.md).
