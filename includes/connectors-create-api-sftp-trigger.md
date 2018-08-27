Pojďme přidat aktivační události.

1. Zadejte *sftp* do vyhledávacího pole v návrháři pro logic apps zvolte **SFTP – když se přidá nebo upraví soubor** aktivační událost   
   ![Obrázek triggeru SFTP 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. **Při přidání nebo změně souboru** otevře se ovládací prvek  
   ![Obrázek triggeru SFTP 2](./media/connectors-create-api-sftp/trigger-2.png)  
3. Vyberte **...**  nachází na pravé straně ovládacího prvku. Otevře se ovládací prvek pro výběr složky  
   ![Obrázek triggeru SFTP 3](./media/connectors-create-api-sftp/action-1.png)  
4. Vyberte **SFTP** vyberte kořenovou složku jako složka ke sledování pro nové nebo upravené soubory. Všimněte si, že kořenové složky je nyní zobrazeno v **složky** ovládacího prvku.  
   ![Obrázek triggeru SFTP 4](./media/connectors-create-api-sftp/action-2.png)   

V tomto okamžiku byla nakonfigurována svou aplikaci logiky s triggerem, který začne upravovat nebo v určité složce protokolu SFTP vytvoří soubor spustit z jiné triggery a akce v pracovním postupu. 

> [!NOTE]
> Pro aplikaci logiky, která byla plně funkční musí obsahovat aspoň jeden trigger a jednu akci. Postupujte podle kroků v další části přidáte akci.  
> 
> 

