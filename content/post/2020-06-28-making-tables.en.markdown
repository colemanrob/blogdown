---
title: Making Tables
author: ''
date: '2020-06-28'
slug: making-tables
categories: []
tags: [Other]
subtitle: ''
summary: ''
authors: []
lastmod: '2020-06-28T15:48:52-04:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

I've wanted to dive into the [gt](https://gt.rstudio.com/index.html) package for making tables for awhile now.  Here is a quick example of a table using data from a tidy tuesday a few weeks back.  It seems my hugo theme is over-writing some of the table css, I'll have to check that out.


```r
library(tidyverse)

library(gt)
```





<style type="text/css">
table>tbody>tr:nth-child(odd)>td, table>tbody>tr:nth-child(odd)>th {
    background-color: #fff;
}
table > tbody > tr:hover > td,
table > tbody > tr:hover > th {
  background-color: #fff;
}
.article-style img, .article-style video {
  height: auto;
  margin-left: auto;
  margin-right: auto;
  margin-top: 0;
  margin-bottom: 0;
  padding: 0;
}
</style>

Let's read in the cocktails recipie


```r
boston_cocktails <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-26/boston_cocktails.csv') %>% 
  janitor::clean_names()
```

Let's make the data reasonable!


```r
boston_tidy <- boston_cocktails %>% 
  # fix the measurements
  separate(measure, into = c('value', 'measurement'), sep = " ", remove = FALSE) %>% 
  # remove values that don't have properly formed measurements crudely
  filter(measurement == "oz") %>% 
  # convert fractions to decimals
  mutate(value = case_when(
    value == "1/2" ~ ".5",
    value == "1/3" ~ ".33",
    value == "1/4" ~ ".25",
    value == "3/4" ~ ".74",
    TRUE ~ value
  )) %>% 
  # magic
  type_convert() %>% 
  # only keep realistic values
  filter(value <= 5)

boston_table <- boston_tidy%>% 
  filter(!category %in% c('Non-alcoholic Drinks', 'Rum', 'Shooters')) %>% 
  group_by(category, name) %>% 
  summarize(total_ingredients = which.max(ingredient_number),
            total_ounces = sum(value)) %>% 
  arrange(category, desc(total_ingredients)) %>% 
  slice_head(n = 5, order_by = total_ingredients) %>% 
  arrange(category) %>% 
  ungroup()
```

make the table


```r
boston_gt_table <- gt(boston_table, 
                      rowname_col = "name",
                      groupname_col = "category")


boston_gt_table <- boston_gt_table %>% 
  tab_header(
    title = "Top cocktails by Category",
    subtitle = "Top 5 cocktails based on number of ingredients"
  ) %>% 
  tab_spanner(label = "Drink Information",
              columns = vars("total_ingredients", "total_ounces")) %>% 
  cols_label(
    total_ingredients = "Total Ingredients",
    total_ounces = "Total Ounces",
    name = "Name"
  ) %>% 
  summary_rows(
    groups = TRUE,
    columns = vars(total_ingredients, total_ounces),
    fns = list(Total = "sum"),
    formatter = fmt_number,
    decimals = 2,
    use_seps = TRUE
  ) 

boston_gt_table %>% 
  tab_source_note(
    source_note = md("Data comes from the [Tidy Tuesday project](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-05-26/readme.md)")
  ) %>% 
  opt_align_table_header(align = "center")
```

<!--html_preserve--><style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#jrihnhzhfn .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#jrihnhzhfn .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#jrihnhzhfn .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#jrihnhzhfn .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 4px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#jrihnhzhfn .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#jrihnhzhfn .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#jrihnhzhfn .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#jrihnhzhfn .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#jrihnhzhfn .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#jrihnhzhfn .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#jrihnhzhfn .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#jrihnhzhfn .gt_group_heading {
  padding: 8px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#jrihnhzhfn .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#jrihnhzhfn .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#jrihnhzhfn .gt_from_md > :first-child {
  margin-top: 0;
}

#jrihnhzhfn .gt_from_md > :last-child {
  margin-bottom: 0;
}

#jrihnhzhfn .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#jrihnhzhfn .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 12px;
}

#jrihnhzhfn .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#jrihnhzhfn .gt_first_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
}

#jrihnhzhfn .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#jrihnhzhfn .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#jrihnhzhfn .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#jrihnhzhfn .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#jrihnhzhfn .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding: 4px;
}

#jrihnhzhfn .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#jrihnhzhfn .gt_sourcenote {
  font-size: 90%;
  padding: 4px;
}

#jrihnhzhfn .gt_left {
  text-align: left;
}

#jrihnhzhfn .gt_center {
  text-align: center;
}

#jrihnhzhfn .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#jrihnhzhfn .gt_font_normal {
  font-weight: normal;
}

#jrihnhzhfn .gt_font_bold {
  font-weight: bold;
}

#jrihnhzhfn .gt_font_italic {
  font-style: italic;
}

#jrihnhzhfn .gt_super {
  font-size: 65%;
}

#jrihnhzhfn .gt_footnote_marks {
  font-style: italic;
  font-size: 65%;
}
</style>
<div id="jrihnhzhfn" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;"><table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="3" class="gt_heading gt_title gt_font_normal" style>Top cocktails by Category</th>
    </tr>
    <tr>
      <th colspan="3" class="gt_heading gt_subtitle gt_font_normal gt_bottom_border" style>Top 5 cocktails based on number of ingredients</th>
    </tr>
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="2" colspan="1"></th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2">
        <span class="gt_column_spanner">Drink Information</span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1">Total Ingredients</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1">Total Ounces</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">Brandy</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Champs Elysees Cocktail</td>
      <td class="gt_row gt_center">5</td>
      <td class="gt_row gt_right">4.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Cherry Blossom</td>
      <td class="gt_row gt_center">5</td>
      <td class="gt_row gt_right">2.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">D'artagnan</td>
      <td class="gt_row gt_center">5</td>
      <td class="gt_row gt_right">6.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Deauville Cocktail</td>
      <td class="gt_row gt_center">5</td>
      <td class="gt_row gt_right">3.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Gilroy Cocktail</td>
      <td class="gt_row gt_center">5</td>
      <td class="gt_row gt_right">3.48</td>
    </tr>
    <tr>
      <td class="gt_row gt_stub gt_right gt_summary_row gt_first_summary_row">Total</td>
      <td class="gt_row gt_center gt_summary_row gt_first_summary_row">25.00</td>
      <td class="gt_row gt_right gt_summary_row gt_first_summary_row">19.48</td>
    </tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">Cocktail Classics</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Eye-Opener</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">6.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Gloom Lifter</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">4.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Hyatt's Jamaican Banana</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">5.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">New Orleans Gin Fizz</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">6.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Prairie Oyster Cocktail</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">6.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_stub gt_right gt_summary_row gt_first_summary_row">Total</td>
      <td class="gt_row gt_center gt_summary_row gt_first_summary_row">30.00</td>
      <td class="gt_row gt_right gt_summary_row gt_first_summary_row">28.00</td>
    </tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">Cordials and Liqueurs</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Praire Oyster Cocktail</td>
      <td class="gt_row gt_center">5</td>
      <td class="gt_row gt_right">5.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Absinthe Special Cocktail</td>
      <td class="gt_row gt_center">2</td>
      <td class="gt_row gt_right">1.25</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Amaretto Rose</td>
      <td class="gt_row gt_center">2</td>
      <td class="gt_row gt_right">1.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Amaretto Sour</td>
      <td class="gt_row gt_center">2</td>
      <td class="gt_row gt_right">1.24</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Amber Amour</td>
      <td class="gt_row gt_center">2</td>
      <td class="gt_row gt_right">0.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_stub gt_right gt_summary_row gt_first_summary_row">Total</td>
      <td class="gt_row gt_center gt_summary_row gt_first_summary_row">13.00</td>
      <td class="gt_row gt_right gt_summary_row gt_first_summary_row">8.99</td>
    </tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">Gin</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">The Winkle</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">11.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Vow Of Silence</td>
      <td class="gt_row gt_center">5</td>
      <td class="gt_row gt_right">2.24</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">The Wink</td>
      <td class="gt_row gt_center">4</td>
      <td class="gt_row gt_right">4.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Water Lily</td>
      <td class="gt_row gt_center">4</td>
      <td class="gt_row gt_right">2.96</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Western Rose</td>
      <td class="gt_row gt_center">4</td>
      <td class="gt_row gt_right">3.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_stub gt_right gt_summary_row gt_first_summary_row">Total</td>
      <td class="gt_row gt_center gt_summary_row gt_first_summary_row">23.00</td>
      <td class="gt_row gt_right gt_summary_row gt_first_summary_row">23.70</td>
    </tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">Rum - Daiquiris</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Hai Karate</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">7.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Ko Adang</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">5.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Zombie</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">7.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Floridita No. 3</td>
      <td class="gt_row gt_center">5</td>
      <td class="gt_row gt_right">4.24</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Hush and Wonder</td>
      <td class="gt_row gt_center">5</td>
      <td class="gt_row gt_right">7.48</td>
    </tr>
    <tr>
      <td class="gt_row gt_stub gt_right gt_summary_row gt_first_summary_row">Total</td>
      <td class="gt_row gt_center gt_summary_row gt_first_summary_row">28.00</td>
      <td class="gt_row gt_right gt_summary_row gt_first_summary_row">30.72</td>
    </tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">Tequila</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Amante Picante</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">10.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Chupa Cabra</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">5.24</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Flower Power</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">9.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Oldest Temptation</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">5.25</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">The Nomad South</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">5.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_stub gt_right gt_summary_row gt_first_summary_row">Total</td>
      <td class="gt_row gt_center gt_summary_row gt_first_summary_row">30.00</td>
      <td class="gt_row gt_right gt_summary_row gt_first_summary_row">35.49</td>
    </tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">Vodka</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Ibiza</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">5.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Long Island Iced Tea</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">4.20</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Purple Passion Iced Tea</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">3.24</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Sonic Blaster</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">4.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Thyme Collins</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">10.00</td>
    </tr>
    <tr>
      <td class="gt_row gt_stub gt_right gt_summary_row gt_first_summary_row">Total</td>
      <td class="gt_row gt_center gt_summary_row gt_first_summary_row">30.00</td>
      <td class="gt_row gt_right gt_summary_row gt_first_summary_row">27.44</td>
    </tr>
    <tr class="gt_group_heading_row">
      <td colspan="3" class="gt_group_heading">Whiskies</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Blarney Stone Cocktail</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">5.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">California Lemonade</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">6.25</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Carre Reprise</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">5.50</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Chi-Town Flip</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">5.47</td>
    </tr>
    <tr>
      <td class="gt_row gt_left gt_stub">Double Standard Sour</td>
      <td class="gt_row gt_center">6</td>
      <td class="gt_row gt_right">3.73</td>
    </tr>
    <tr>
      <td class="gt_row gt_stub gt_right gt_summary_row gt_first_summary_row">Total</td>
      <td class="gt_row gt_center gt_summary_row gt_first_summary_row">30.00</td>
      <td class="gt_row gt_right gt_summary_row gt_first_summary_row">26.45</td>
    </tr>
  </tbody>
  <tfoot class="gt_sourcenotes">
    <tr>
      <td class="gt_sourcenote" colspan="3">Data comes from the <a href="https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-05-26/readme.md">Tidy Tuesday project</a></td>
    </tr>
  </tfoot>
  
</table></div><!--/html_preserve-->

More to come!
