## Application Using Main Window
create a MainWindow based application using the designer
- [Introduction](#INTRO)
- [SpreadSheet](#SpreadSheet)
- [TextEditor](#TextEditor)

<div id = "back"></div>


    
## **Introduction** 

<a name="INTRO"></a>

"" ** “Programs must be written for people to read, and only incidentally for machines to execute.” ** ""

**QMainWindow** is a main window provides a framework for building an application's user interface. Qt has QMainWindow and its related classes for main window management. QMainWindow has its own layout to which you can add QToolBars, QDockWidgets, a QMenuBar, and a QStatusBar. The layout has a center area that can be occupied by any kind of widget. You can see an image of the layout below.

![Image](/qmainwindoww.png)

**Qt Designer** is the Qt tool for designing and building graphical user interfaces (GUIs) with Qt Widgets. You can compose and customize your windows or dialogs in a what-you-see-is-what-you-get (WYSIWYG) manner, and test them using different styles and resolutions.

Widgets and forms created with Qt Designer integrate seamlessly with programmed code, using Qt's signals and slots mechanism, so that you can easily assign behavior to graphical elements. All properties set in Qt Designer can be changed dynamically within the code. Furthermore, features like widget promotion and custom plugins allow you to use your own components with Qt Designer.

![Image](/qtdesigner.png)

 >**In This Zip you will have the project** [Homework3.zip]() 


### SpreadSheet 
<a name="SpreadSheet"></a>
We wrote the code for the graphical and set of actions for our main SpreadSheet application, now we will write a set of basic functionality.
we will start with the connections made for spreadsheet:
1.the **updateStatusBar** takes two ints in order to synchronize with the selected item from spreadsheet, it changes the cellLocation text with the current cell coordinate.
```javascript
void updateStatusBar(int, int); //Respond for the call changed
```
Here is the implementation of this function
```javascript
void SpreadSheet::updateStatusBar(int row, int col)
{
    QString cell{"(%0, %1)"};
   cellLocation->setText(cell.arg(row+1).arg(col+1));

}
```
2.we add the function for the **gocell** action,we created a Dialog for the user to select a cell, for that, first we created a Form Class, the using the designer we obtain the form of cell location, in addition we added the regular expression validator for the lineEdit , and finally we added a public getter for the line edit text to get the cell address.
Here is the Form of Cell location:
[!Image](/cellLocation.png)
Here is the code of the regular expression validator and the add of the pubic Getter
```javascript

    ui(new Ui::GoDialog)
{
    ui->setupUi(this);
    //Définir l'expression nregulière
    QRegExp reg{"[A-Z][1-9][0-9]"};
    //validateur pour le lineedit
    ui->lineEdit_2->setValidator(new QRegExpValidator(reg));

}
QString GoDialog::getText()const
{
    return ui->lineEdit_2->text();
}

```


We added the **makeConnexion()** to connect all the actions.
here is the content of this Function:
```javascript
void SpreadSheet::makeConnexions()
{

   // --------- Connexion for the  select all action ----/
   connect(all, &QAction::triggered,spreadsheet, &QTableWidget::selectAll);
   connect(row, &QAction::triggered,spreadsheet, &QTableWidget::selectRow);
   connect(Column, &QAction::triggered, spreadsheet, &QTableWidget::selectColumn);
   //connection for the clear all 
   connect(deleteall, &QAction::triggered,spreadsheet, &QTableWidget::clearContents);
   //connection for the delete cell content
   connect(deleteAction, &QAction::triggered,this, &SpreadSheet::deletecell);
   // Connection for the  show grid
   connect(showGrid, &QAction::triggered,spreadsheet, &QTableWidget::setShowGrid);
   //Connection for the exit button
   connect(exit, &QAction::triggered, this, &SpreadSheet::close);
   //connectting the chane of any element in the spreadsheet with the update status bar
   connect(spreadsheet, &QTableWidget::cellClicked, this,  &SpreadSheet::updateStatusBar);
   //connection entre le go et le slot
   connect(goCell,&QAction::triggered,this,&SpreadSheet::GotoCellSlots);
   connect(find,&QAction::triggered,this,&SpreadSheet::Search);
   //connection de save file
   connect(save, &QAction::triggered,this,&SpreadSheet::saveslot);
   //connection du open file
   connect(open,&QAction::triggered,this,&SpreadSheet::Open);
   //connection du new file
   connect(newFile,&QAction::triggered,this,&SpreadSheet::newfile);
   //connect du slot
   connect(about,&QAction::triggered,this,&SpreadSheet::aboutSlot);
   //connect du Qtslot
   connect(aboutQt,&QAction::triggered,this,&SpreadSheet::aboutQtSlot);
   //connect du recent files
   connect(recfiles,&QAction::triggered,this,&SpreadSheet::recentfile);
   //connect saveas
   connect(saveAs,&QAction::triggered,this,&SpreadSheet::SaveAsSlot);
}
```
Finally, we obtain:

![Image](/spread1.png)
![Image](/spread2.png)
SpreadSheet Application with actions and menus 
### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/HarirFahem/HomeWork3/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
