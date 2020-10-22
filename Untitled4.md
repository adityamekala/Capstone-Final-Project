```python
#The idea of this study is to help people planning to open a new restaurant in Toronto to chose the right location by providing data about the income and population of each neighborhood as well as the competitors already present on the same r regions.
#To provide the stakeholders the necessary information I'll be combining Toronto's 2016 Census that contains Population, Average income per Neighborhood with Foursquare API to collect competitors on the same neighborhoods.
#Toronto's Census data in publicly available at this website: https://www.toronto.ca/city-government/data-research-maps/open-data/open-data-catalogue/#8c732154-5012-9afe-d0cd-ba3ffc813d5a
import numpy as np # library to handle data in a vectorized manner

import pandas as pd # library for data analsysis
pd.set_option('display.max_columns', None)
pd.set_option('display.max_rows', None)

!conda install -c conda-forge geopy --yes # uncomment this line if you haven't completed the Foursquare API lab
from geopy.geocoders import Nominatim # convert an address into latitude and longitude values

!conda install -c conda-forge folium=0.5.0 --yes # uncomment this line if you haven't completed the Foursquare API lab
import folium # map rendering library

print('Libraries imported.')
```


```python
# Toronto Open Data Catalogue - Neighbourhood Profiles 2016 (CSV)
# https://www.toronto.ca/city-government/data-research-maps/open-data/open-data-catalogue/#8c732154-5012-9afe-d0cd-ba3ffc813d5a

csv_path='https://www.toronto.ca/ext/open_data/catalog/data_set_files/2016_neighbourhood_profiles.csv'
df = pd.read_csv(csv_path,encoding='latin1')
print('Data loaded')
```

    Data loaded
    


```python

df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Category</th>
      <th>Topic</th>
      <th>Data Source</th>
      <th>Characteristic</th>
      <th>City of Toronto</th>
      <th>Agincourt North</th>
      <th>Agincourt South-Malvern West</th>
      <th>Alderwood</th>
      <th>Annex</th>
      <th>Banbury-Don Mills</th>
      <th>Bathurst Manor</th>
      <th>Bay Street Corridor</th>
      <th>Bayview Village</th>
      <th>Bayview Woods-Steeles</th>
      <th>Bedford Park-Nortown</th>
      <th>Beechborough-Greenbrook</th>
      <th>Bendale</th>
      <th>Birchcliffe-Cliffside</th>
      <th>Black Creek</th>
      <th>Blake-Jones</th>
      <th>Briar Hill-Belgravia</th>
      <th>Bridle Path-Sunnybrook-York Mills</th>
      <th>Broadview North</th>
      <th>Brookhaven-Amesbury</th>
      <th>Cabbagetown-South St. James Town</th>
      <th>Caledonia-Fairbank</th>
      <th>Casa Loma</th>
      <th>Centennial Scarborough</th>
      <th>Church-Yonge Corridor</th>
      <th>Clairlea-Birchmount</th>
      <th>Clanton Park</th>
      <th>Cliffcrest</th>
      <th>Corso Italia-Davenport</th>
      <th>Danforth</th>
      <th>Danforth East York</th>
      <th>Don Valley Village</th>
      <th>Dorset Park</th>
      <th>Dovercourt-Wallace Emerson-Junction</th>
      <th>Downsview-Roding-CFB</th>
      <th>Dufferin Grove</th>
      <th>East End-Danforth</th>
      <th>Edenbridge-Humber Valley</th>
      <th>Eglinton East</th>
      <th>Elms-Old Rexdale</th>
      <th>Englemount-Lawrence</th>
      <th>Eringate-Centennial-West Deane</th>
      <th>Etobicoke West Mall</th>
      <th>Flemingdon Park</th>
      <th>Forest Hill North</th>
      <th>Forest Hill South</th>
      <th>Glenfield-Jane Heights</th>
      <th>Greenwood-Coxwell</th>
      <th>Guildwood</th>
      <th>Henry Farm</th>
      <th>High Park North</th>
      <th>High Park-Swansea</th>
      <th>Highland Creek</th>
      <th>Hillcrest Village</th>
      <th>Humber Heights-Westmount</th>
      <th>Humber Summit</th>
      <th>Humbermede</th>
      <th>Humewood-Cedarvale</th>
      <th>Ionview</th>
      <th>Islington-City Centre West</th>
      <th>Junction Area</th>
      <th>Keelesdale-Eglinton West</th>
      <th>Kennedy Park</th>
      <th>Kensington-Chinatown</th>
      <th>Kingsview Village-The Westway</th>
      <th>Kingsway South</th>
      <th>Lambton Baby Point</th>
      <th>L'Amoreaux</th>
      <th>Lansing-Westgate</th>
      <th>Lawrence Park North</th>
      <th>Lawrence Park South</th>
      <th>Leaside-Bennington</th>
      <th>Little Portugal</th>
      <th>Long Branch</th>
      <th>Malvern</th>
      <th>Maple Leaf</th>
      <th>Markland Wood</th>
      <th>Milliken</th>
      <th>Mimico (includes Humber Bay Shores)</th>
      <th>Morningside</th>
      <th>Moss Park</th>
      <th>Mount Dennis</th>
      <th>Mount Olive-Silverstone-Jamestown</th>
      <th>Mount Pleasant East</th>
      <th>Mount Pleasant West</th>
      <th>New Toronto</th>
      <th>Newtonbrook East</th>
      <th>Newtonbrook West</th>
      <th>Niagara</th>
      <th>North Riverdale</th>
      <th>North St. James Town</th>
      <th>Oakridge</th>
      <th>Oakwood Village</th>
      <th>O'Connor-Parkview</th>
      <th>Old East York</th>
      <th>Palmerston-Little Italy</th>
      <th>Parkwoods-Donalda</th>
      <th>Pelmo Park-Humberlea</th>
      <th>Playter Estates-Danforth</th>
      <th>Pleasant View</th>
      <th>Princess-Rosethorn</th>
      <th>Regent Park</th>
      <th>Rexdale-Kipling</th>
      <th>Rockcliffe-Smythe</th>
      <th>Roncesvalles</th>
      <th>Rosedale-Moore Park</th>
      <th>Rouge</th>
      <th>Runnymede-Bloor West Village</th>
      <th>Rustic</th>
      <th>Scarborough Village</th>
      <th>South Parkdale</th>
      <th>South Riverdale</th>
      <th>St.Andrew-Windfields</th>
      <th>Steeles</th>
      <th>Stonegate-Queensway</th>
      <th>Tam O'Shanter-Sullivan</th>
      <th>Taylor-Massey</th>
      <th>The Beaches</th>
      <th>Thistletown-Beaumond Heights</th>
      <th>Thorncliffe Park</th>
      <th>Trinity-Bellwoods</th>
      <th>University</th>
      <th>Victoria Village</th>
      <th>Waterfront Communities-The Island</th>
      <th>West Hill</th>
      <th>West Humber-Clairville</th>
      <th>Westminster-Branson</th>
      <th>Weston</th>
      <th>Weston-Pelham Park</th>
      <th>Wexford/Maryvale</th>
      <th>Willowdale East</th>
      <th>Willowdale West</th>
      <th>Willowridge-Martingrove-Richview</th>
      <th>Woburn</th>
      <th>Woodbine Corridor</th>
      <th>Woodbine-Lumsden</th>
      <th>Wychwood</th>
      <th>Yonge-Eglinton</th>
      <th>Yonge-St.Clair</th>
      <th>York University Heights</th>
      <th>Yorkdale-Glen Park</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Neighbourhood Information</td>
      <td>Neighbourhood Information</td>
      <td>City of Toronto</td>
      <td>Neighbourhood Number</td>
      <td>NaN</td>
      <td>129</td>
      <td>128</td>
      <td>20</td>
      <td>95</td>
      <td>42</td>
      <td>34</td>
      <td>76</td>
      <td>52</td>
      <td>49</td>
      <td>39</td>
      <td>112</td>
      <td>127</td>
      <td>122</td>
      <td>24</td>
      <td>69</td>
      <td>108</td>
      <td>41</td>
      <td>57</td>
      <td>30</td>
      <td>71</td>
      <td>109</td>
      <td>96</td>
      <td>133</td>
      <td>75</td>
      <td>120</td>
      <td>33</td>
      <td>123</td>
      <td>92</td>
      <td>66</td>
      <td>59</td>
      <td>47</td>
      <td>126</td>
      <td>93</td>
      <td>26</td>
      <td>83</td>
      <td>62</td>
      <td>9</td>
      <td>138</td>
      <td>5</td>
      <td>32</td>
      <td>11</td>
      <td>13</td>
      <td>44</td>
      <td>102</td>
      <td>101</td>
      <td>25</td>
      <td>65</td>
      <td>140</td>
      <td>53</td>
      <td>88</td>
      <td>87</td>
      <td>134</td>
      <td>48</td>
      <td>8</td>
      <td>21</td>
      <td>22</td>
      <td>106</td>
      <td>125</td>
      <td>14</td>
      <td>90</td>
      <td>110</td>
      <td>124</td>
      <td>78</td>
      <td>6</td>
      <td>15</td>
      <td>114</td>
      <td>117</td>
      <td>38</td>
      <td>105</td>
      <td>103</td>
      <td>56</td>
      <td>84</td>
      <td>19</td>
      <td>132</td>
      <td>29</td>
      <td>12</td>
      <td>130</td>
      <td>17</td>
      <td>135</td>
      <td>73</td>
      <td>115</td>
      <td>2</td>
      <td>99</td>
      <td>104</td>
      <td>18</td>
      <td>50</td>
      <td>36</td>
      <td>82</td>
      <td>68</td>
      <td>74</td>
      <td>121</td>
      <td>107</td>
      <td>54</td>
      <td>58</td>
      <td>80</td>
      <td>45</td>
      <td>23</td>
      <td>67</td>
      <td>46</td>
      <td>10</td>
      <td>72</td>
      <td>4</td>
      <td>111</td>
      <td>86</td>
      <td>98</td>
      <td>131</td>
      <td>89</td>
      <td>28</td>
      <td>139</td>
      <td>85</td>
      <td>70</td>
      <td>40</td>
      <td>116</td>
      <td>16</td>
      <td>118</td>
      <td>61</td>
      <td>63</td>
      <td>3</td>
      <td>55</td>
      <td>81</td>
      <td>79</td>
      <td>43</td>
      <td>77</td>
      <td>136</td>
      <td>1</td>
      <td>35</td>
      <td>113</td>
      <td>91</td>
      <td>119</td>
      <td>51</td>
      <td>37</td>
      <td>7</td>
      <td>137</td>
      <td>64</td>
      <td>60</td>
      <td>94</td>
      <td>100</td>
      <td>97</td>
      <td>27</td>
      <td>31</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Neighbourhood Information</td>
      <td>Neighbourhood Information</td>
      <td>City of Toronto</td>
      <td>TSNS2020 Designation</td>
      <td>NaN</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>Emerging Neighbourhood</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>NIA</td>
      <td>Emerging Neighbourhood</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>Emerging Neighbourhood</td>
      <td>NIA</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>Emerging Neighbourhood</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>Emerging Neighbourhood</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>NIA</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>Emerging Neighbourhood</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>Emerging Neighbourhood</td>
      <td>NIA</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>No Designation</td>
      <td>NIA</td>
      <td>Emerging Neighbourhood</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Population</td>
      <td>Population and dwellings</td>
      <td>Census Profile 98-316-X2016001</td>
      <td>Population, 2016</td>
      <td>2,731,571</td>
      <td>29,113</td>
      <td>23,757</td>
      <td>12,054</td>
      <td>30,526</td>
      <td>27,695</td>
      <td>15,873</td>
      <td>25,797</td>
      <td>21,396</td>
      <td>13,154</td>
      <td>23,236</td>
      <td>6,577</td>
      <td>29,960</td>
      <td>22,291</td>
      <td>21,737</td>
      <td>7,727</td>
      <td>14,257</td>
      <td>9,266</td>
      <td>11,499</td>
      <td>17,757</td>
      <td>11,669</td>
      <td>9,955</td>
      <td>10,968</td>
      <td>13,362</td>
      <td>31,340</td>
      <td>26,984</td>
      <td>16,472</td>
      <td>15,935</td>
      <td>14,133</td>
      <td>9,666</td>
      <td>17,180</td>
      <td>27,051</td>
      <td>25,003</td>
      <td>36,625</td>
      <td>35,052</td>
      <td>11,785</td>
      <td>21,381</td>
      <td>15,535</td>
      <td>22,776</td>
      <td>9,456</td>
      <td>22,372</td>
      <td>18,588</td>
      <td>11,848</td>
      <td>21,933</td>
      <td>12,806</td>
      <td>10,732</td>
      <td>30,491</td>
      <td>14,417</td>
      <td>9,917</td>
      <td>15,723</td>
      <td>22,162</td>
      <td>23,925</td>
      <td>12,494</td>
      <td>16,934</td>
      <td>10,948</td>
      <td>12,416</td>
      <td>15,545</td>
      <td>14,365</td>
      <td>13,641</td>
      <td>43,965</td>
      <td>14,366</td>
      <td>11,058</td>
      <td>17,123</td>
      <td>17,945</td>
      <td>22,000</td>
      <td>9,271</td>
      <td>7,985</td>
      <td>43,993</td>
      <td>16,164</td>
      <td>14,607</td>
      <td>15,179</td>
      <td>16,828</td>
      <td>15,559</td>
      <td>10,084</td>
      <td>43,794</td>
      <td>10,111</td>
      <td>10,554</td>
      <td>26,572</td>
      <td>33,964</td>
      <td>17,455</td>
      <td>20,506</td>
      <td>13,593</td>
      <td>32,954</td>
      <td>16,775</td>
      <td>29,658</td>
      <td>11,463</td>
      <td>16,097</td>
      <td>23,831</td>
      <td>31,180</td>
      <td>11,916</td>
      <td>18,615</td>
      <td>13,845</td>
      <td>21,210</td>
      <td>18,675</td>
      <td>9,233</td>
      <td>13,826</td>
      <td>34,805</td>
      <td>10,722</td>
      <td>7,804</td>
      <td>15,818</td>
      <td>11,051</td>
      <td>10,803</td>
      <td>10,529</td>
      <td>22,246</td>
      <td>14,974</td>
      <td>20,923</td>
      <td>46,496</td>
      <td>10,070</td>
      <td>9,941</td>
      <td>16,724</td>
      <td>21,849</td>
      <td>27,876</td>
      <td>17,812</td>
      <td>24,623</td>
      <td>25,051</td>
      <td>27,446</td>
      <td>15,683</td>
      <td>21,567</td>
      <td>10,360</td>
      <td>21,108</td>
      <td>16,556</td>
      <td>7,607</td>
      <td>17,510</td>
      <td>65,913</td>
      <td>27,392</td>
      <td>33,312</td>
      <td>26,274</td>
      <td>17,992</td>
      <td>11,098</td>
      <td>27,917</td>
      <td>50,434</td>
      <td>16,936</td>
      <td>22,156</td>
      <td>53,485</td>
      <td>12,541</td>
      <td>7,865</td>
      <td>14,349</td>
      <td>11,817</td>
      <td>12,528</td>
      <td>27,593</td>
      <td>14,804</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Population</td>
      <td>Population and dwellings</td>
      <td>Census Profile 98-316-X2016001</td>
      <td>Population, 2011</td>
      <td>2,615,060</td>
      <td>30,279</td>
      <td>21,988</td>
      <td>11,904</td>
      <td>29,177</td>
      <td>26,918</td>
      <td>15,434</td>
      <td>19,348</td>
      <td>17,671</td>
      <td>13,530</td>
      <td>23,185</td>
      <td>6,488</td>
      <td>27,876</td>
      <td>21,856</td>
      <td>22,057</td>
      <td>7,763</td>
      <td>14,302</td>
      <td>8,713</td>
      <td>11,563</td>
      <td>17,787</td>
      <td>12,053</td>
      <td>9,851</td>
      <td>10,487</td>
      <td>13,093</td>
      <td>28,349</td>
      <td>24,770</td>
      <td>14,612</td>
      <td>15,703</td>
      <td>13,743</td>
      <td>9,444</td>
      <td>16,712</td>
      <td>26,739</td>
      <td>24,363</td>
      <td>34,631</td>
      <td>34,659</td>
      <td>11,449</td>
      <td>20,839</td>
      <td>14,943</td>
      <td>22,829</td>
      <td>9,550</td>
      <td>22,086</td>
      <td>18,810</td>
      <td>10,927</td>
      <td>22,168</td>
      <td>12,474</td>
      <td>10,926</td>
      <td>31,390</td>
      <td>14,083</td>
      <td>9,816</td>
      <td>11,333</td>
      <td>21,292</td>
      <td>21,740</td>
      <td>13,097</td>
      <td>17,656</td>
      <td>10,583</td>
      <td>12,525</td>
      <td>15,853</td>
      <td>14,108</td>
      <td>13,091</td>
      <td>38,084</td>
      <td>14,027</td>
      <td>10,638</td>
      <td>17,058</td>
      <td>18,495</td>
      <td>21,723</td>
      <td>9,170</td>
      <td>7,921</td>
      <td>44,919</td>
      <td>14,642</td>
      <td>14,541</td>
      <td>15,070</td>
      <td>17,011</td>
      <td>12,050</td>
      <td>9,632</td>
      <td>45,086</td>
      <td>10,197</td>
      <td>10,436</td>
      <td>27,167</td>
      <td>26,541</td>
      <td>17,587</td>
      <td>16,306</td>
      <td>13,145</td>
      <td>32,788</td>
      <td>15,982</td>
      <td>28,593</td>
      <td>10,900</td>
      <td>16,423</td>
      <td>23,052</td>
      <td>21,274</td>
      <td>12,191</td>
      <td>17,832</td>
      <td>13,497</td>
      <td>21,073</td>
      <td>18,316</td>
      <td>9,118</td>
      <td>13,746</td>
      <td>34,617</td>
      <td>8,710</td>
      <td>7,653</td>
      <td>16,144</td>
      <td>11,197</td>
      <td>10,007</td>
      <td>10,488</td>
      <td>22,267</td>
      <td>15,050</td>
      <td>20,631</td>
      <td>45,912</td>
      <td>9,632</td>
      <td>9,951</td>
      <td>16,609</td>
      <td>21,251</td>
      <td>25,642</td>
      <td>17,958</td>
      <td>25,017</td>
      <td>24,691</td>
      <td>27,398</td>
      <td>15,594</td>
      <td>21,130</td>
      <td>10,138</td>
      <td>19,225</td>
      <td>16,802</td>
      <td>7,782</td>
      <td>17,182</td>
      <td>43,361</td>
      <td>26,547</td>
      <td>34,100</td>
      <td>25,446</td>
      <td>18,170</td>
      <td>12,010</td>
      <td>27,018</td>
      <td>45,041</td>
      <td>15,004</td>
      <td>21,343</td>
      <td>53,350</td>
      <td>11,703</td>
      <td>7,826</td>
      <td>13,986</td>
      <td>10,578</td>
      <td>11,652</td>
      <td>27,713</td>
      <td>14,687</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Population</td>
      <td>Population and dwellings</td>
      <td>Census Profile 98-316-X2016001</td>
      <td>Population Change 2011-2016</td>
      <td>4.50%</td>
      <td>-3.90%</td>
      <td>8.00%</td>
      <td>1.30%</td>
      <td>4.60%</td>
      <td>2.90%</td>
      <td>2.80%</td>
      <td>33.30%</td>
      <td>21.10%</td>
      <td>-2.80%</td>
      <td>0.20%</td>
      <td>1.40%</td>
      <td>7.50%</td>
      <td>2.00%</td>
      <td>-1.50%</td>
      <td>-0.50%</td>
      <td>-0.30%</td>
      <td>6.30%</td>
      <td>-0.60%</td>
      <td>-0.20%</td>
      <td>-3.20%</td>
      <td>1.10%</td>
      <td>4.60%</td>
      <td>2.10%</td>
      <td>10.60%</td>
      <td>8.90%</td>
      <td>12.70%</td>
      <td>1.50%</td>
      <td>2.80%</td>
      <td>2.40%</td>
      <td>2.80%</td>
      <td>1.20%</td>
      <td>2.60%</td>
      <td>5.80%</td>
      <td>1.10%</td>
      <td>2.90%</td>
      <td>2.60%</td>
      <td>4.00%</td>
      <td>-0.20%</td>
      <td>-1.00%</td>
      <td>1.30%</td>
      <td>-1.20%</td>
      <td>8.40%</td>
      <td>-1.10%</td>
      <td>2.70%</td>
      <td>-1.80%</td>
      <td>-2.90%</td>
      <td>2.40%</td>
      <td>1.00%</td>
      <td>38.70%</td>
      <td>4.10%</td>
      <td>10.10%</td>
      <td>-4.60%</td>
      <td>-4.10%</td>
      <td>3.40%</td>
      <td>-0.90%</td>
      <td>-1.90%</td>
      <td>1.80%</td>
      <td>4.20%</td>
      <td>15.40%</td>
      <td>2.40%</td>
      <td>3.90%</td>
      <td>0.40%</td>
      <td>-3.00%</td>
      <td>1.30%</td>
      <td>1.10%</td>
      <td>0.80%</td>
      <td>-2.10%</td>
      <td>10.40%</td>
      <td>0.50%</td>
      <td>0.70%</td>
      <td>-1.10%</td>
      <td>29.10%</td>
      <td>4.70%</td>
      <td>-2.90%</td>
      <td>-0.80%</td>
      <td>1.10%</td>
      <td>-2.20%</td>
      <td>28.00%</td>
      <td>-0.80%</td>
      <td>25.80%</td>
      <td>3.40%</td>
      <td>0.50%</td>
      <td>5.00%</td>
      <td>3.70%</td>
      <td>5.20%</td>
      <td>-2.00%</td>
      <td>3.40%</td>
      <td>46.60%</td>
      <td>-2.30%</td>
      <td>4.40%</td>
      <td>2.60%</td>
      <td>0.70%</td>
      <td>2.00%</td>
      <td>1.30%</td>
      <td>0.60%</td>
      <td>0.50%</td>
      <td>23.10%</td>
      <td>2.00%</td>
      <td>-2.00%</td>
      <td>-1.30%</td>
      <td>8.00%</td>
      <td>0.40%</td>
      <td>-0.10%</td>
      <td>-0.50%</td>
      <td>1.40%</td>
      <td>1.30%</td>
      <td>4.50%</td>
      <td>-0.10%</td>
      <td>0.70%</td>
      <td>2.80%</td>
      <td>8.70%</td>
      <td>-0.80%</td>
      <td>-1.60%</td>
      <td>1.50%</td>
      <td>0.20%</td>
      <td>0.60%</td>
      <td>2.10%</td>
      <td>2.20%</td>
      <td>9.80%</td>
      <td>-1.50%</td>
      <td>-2.20%</td>
      <td>1.90%</td>
      <td>52.00%</td>
      <td>3.20%</td>
      <td>-2.30%</td>
      <td>3.30%</td>
      <td>-1.00%</td>
      <td>-7.60%</td>
      <td>3.30%</td>
      <td>12.00%</td>
      <td>12.90%</td>
      <td>3.80%</td>
      <td>0.30%</td>
      <td>7.20%</td>
      <td>0.50%</td>
      <td>2.60%</td>
      <td>11.70%</td>
      <td>7.50%</td>
      <td>-0.40%</td>
      <td>0.80%</td>
    </tr>
  </tbody>
</table>
</div>




```python
Neighbourhoods = list(df.columns.values)
Neighbourhoods = Neighbourhoods[5:]
print(Neighbourhoods)
```

    ['Agincourt North', 'Agincourt South-Malvern West', 'Alderwood', 'Annex', 'Banbury-Don Mills', 'Bathurst Manor', 'Bay Street Corridor', 'Bayview Village', 'Bayview Woods-Steeles', 'Bedford Park-Nortown', 'Beechborough-Greenbrook', 'Bendale', 'Birchcliffe-Cliffside', 'Black Creek', 'Blake-Jones', 'Briar Hill-Belgravia', 'Bridle Path-Sunnybrook-York Mills', 'Broadview North', 'Brookhaven-Amesbury', 'Cabbagetown-South St. James Town', 'Caledonia-Fairbank', 'Casa Loma', 'Centennial Scarborough', 'Church-Yonge Corridor', 'Clairlea-Birchmount', 'Clanton Park', 'Cliffcrest', 'Corso Italia-Davenport', 'Danforth', 'Danforth East York', 'Don Valley Village', 'Dorset Park', 'Dovercourt-Wallace Emerson-Junction', 'Downsview-Roding-CFB', 'Dufferin Grove', 'East End-Danforth', 'Edenbridge-Humber Valley', 'Eglinton East', 'Elms-Old Rexdale', 'Englemount-Lawrence', 'Eringate-Centennial-West Deane', 'Etobicoke West Mall', 'Flemingdon Park', 'Forest Hill North', 'Forest Hill South', 'Glenfield-Jane Heights', 'Greenwood-Coxwell', 'Guildwood', 'Henry Farm', 'High Park North', 'High Park-Swansea', 'Highland Creek', 'Hillcrest Village', 'Humber Heights-Westmount', 'Humber Summit', 'Humbermede', 'Humewood-Cedarvale', 'Ionview', 'Islington-City Centre West', 'Junction Area', 'Keelesdale-Eglinton West', 'Kennedy Park', 'Kensington-Chinatown', 'Kingsview Village-The Westway', 'Kingsway South', 'Lambton Baby Point', "L'Amoreaux", 'Lansing-Westgate', 'Lawrence Park North', 'Lawrence Park South', 'Leaside-Bennington', 'Little Portugal', 'Long Branch', 'Malvern', 'Maple Leaf', 'Markland Wood', 'Milliken', 'Mimico (includes Humber Bay Shores)', 'Morningside', 'Moss Park', 'Mount Dennis', 'Mount Olive-Silverstone-Jamestown', 'Mount Pleasant East', 'Mount Pleasant West', 'New Toronto', 'Newtonbrook East', 'Newtonbrook West', 'Niagara', 'North Riverdale', 'North St. James Town', 'Oakridge', 'Oakwood Village', "O'Connor-Parkview", 'Old East York', 'Palmerston-Little Italy', 'Parkwoods-Donalda', 'Pelmo Park-Humberlea', 'Playter Estates-Danforth', 'Pleasant View', 'Princess-Rosethorn', 'Regent Park', 'Rexdale-Kipling', 'Rockcliffe-Smythe', 'Roncesvalles', 'Rosedale-Moore Park', 'Rouge', 'Runnymede-Bloor West Village', 'Rustic', 'Scarborough Village', 'South Parkdale', 'South Riverdale', 'St.Andrew-Windfields', 'Steeles', 'Stonegate-Queensway', "Tam O'Shanter-Sullivan", 'Taylor-Massey', 'The Beaches', 'Thistletown-Beaumond Heights', 'Thorncliffe Park', 'Trinity-Bellwoods', 'University', 'Victoria Village', 'Waterfront Communities-The Island', 'West Hill', 'West Humber-Clairville', 'Westminster-Branson', 'Weston', 'Weston-Pelham Park', 'Wexford/Maryvale', 'Willowdale East', 'Willowdale West', 'Willowridge-Martingrove-Richview', 'Woburn', 'Woodbine Corridor', 'Woodbine-Lumsden', 'Wychwood', 'Yonge-Eglinton', 'Yonge-St.Clair', 'York University Heights', 'Yorkdale-Glen Park']
    


```python
dfToronto = pd.DataFrame(index=Neighbourhoods, columns=["Population_2016","Income_2016"])
dfToronto.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Population_2016</th>
      <th>Income_2016</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Agincourt North</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Agincourt South-Malvern West</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Alderwood</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Annex</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Banbury-Don Mills</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Population_2016 = Population, 2016
# Income_2016 = Total income: Average amount ($)


for index, row in dfToronto.iterrows():
    dfToronto.at[index, 'Population_2016'] = df[index][2]
    dfToronto.at[index, 'Income_2016'] = df[index][2264]
    

dfToronto.sort_values('Income_2016')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Population_2016</th>
      <th>Income_2016</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>St.Andrew-Windfields</th>
      <td>17,812</td>
      <td>100,516</td>
    </tr>
    <tr>
      <th>Edenbridge-Humber Valley</th>
      <td>15,535</td>
      <td>101,551</td>
    </tr>
    <tr>
      <th>Lawrence Park North</th>
      <td>14,607</td>
      <td>111,730</td>
    </tr>
    <tr>
      <th>Annex</th>
      <td>30,526</td>
      <td>112,766</td>
    </tr>
    <tr>
      <th>Yonge-St.Clair</th>
      <td>12,528</td>
      <td>114,174</td>
    </tr>
    <tr>
      <th>Bedford Park-Nortown</th>
      <td>23,236</td>
      <td>123,077</td>
    </tr>
    <tr>
      <th>Leaside-Bennington</th>
      <td>16,828</td>
      <td>125,564</td>
    </tr>
    <tr>
      <th>Kingsway South</th>
      <td>9,271</td>
      <td>144,642</td>
    </tr>
    <tr>
      <th>Casa Loma</th>
      <td>10,968</td>
      <td>165,047</td>
    </tr>
    <tr>
      <th>Lawrence Park South</th>
      <td>15,179</td>
      <td>169,203</td>
    </tr>
    <tr>
      <th>Forest Hill South</th>
      <td>10,732</td>
      <td>204,521</td>
    </tr>
    <tr>
      <th>Rosedale-Moore Park</th>
      <td>20,923</td>
      <td>207,903</td>
    </tr>
    <tr>
      <th>Black Creek</th>
      <td>21,737</td>
      <td>25,989</td>
    </tr>
    <tr>
      <th>Mount Olive-Silverstone-Jamestown</th>
      <td>32,954</td>
      <td>26,548</td>
    </tr>
    <tr>
      <th>Oakridge</th>
      <td>13,845</td>
      <td>26,793</td>
    </tr>
    <tr>
      <th>Glenfield-Jane Heights</th>
      <td>30,491</td>
      <td>27,984</td>
    </tr>
    <tr>
      <th>Milliken</th>
      <td>26,572</td>
      <td>28,085</td>
    </tr>
    <tr>
      <th>Flemingdon Park</th>
      <td>21,933</td>
      <td>28,654</td>
    </tr>
    <tr>
      <th>Thorncliffe Park</th>
      <td>21,108</td>
      <td>28,875</td>
    </tr>
    <tr>
      <th>Humbermede</th>
      <td>15,545</td>
      <td>29,528</td>
    </tr>
    <tr>
      <th>Malvern</th>
      <td>43,794</td>
      <td>29,573</td>
    </tr>
    <tr>
      <th>York University Heights</th>
      <td>27,593</td>
      <td>29,958</td>
    </tr>
    <tr>
      <th>Eglinton East</th>
      <td>22,776</td>
      <td>30,033</td>
    </tr>
    <tr>
      <th>Agincourt North</th>
      <td>29,113</td>
      <td>30,414</td>
    </tr>
    <tr>
      <th>Taylor-Massey</th>
      <td>15,683</td>
      <td>30,430</td>
    </tr>
    <tr>
      <th>Humber Summit</th>
      <td>12,416</td>
      <td>30,731</td>
    </tr>
    <tr>
      <th>Mount Dennis</th>
      <td>13,593</td>
      <td>30,827</td>
    </tr>
    <tr>
      <th>Woburn</th>
      <td>53,485</td>
      <td>30,878</td>
    </tr>
    <tr>
      <th>Kennedy Park</th>
      <td>17,123</td>
      <td>30,974</td>
    </tr>
    <tr>
      <th>Bridle Path-Sunnybrook-York Mills</th>
      <td>9,266</td>
      <td>308,010</td>
    </tr>
    <tr>
      <th>Ionview</th>
      <td>13,641</td>
      <td>31,383</td>
    </tr>
    <tr>
      <th>Dorset Park</th>
      <td>25,003</td>
      <td>31,692</td>
    </tr>
    <tr>
      <th>West Humber-Clairville</th>
      <td>33,312</td>
      <td>31,771</td>
    </tr>
    <tr>
      <th>Steeles</th>
      <td>24,623</td>
      <td>31,786</td>
    </tr>
    <tr>
      <th>Rustic</th>
      <td>9,941</td>
      <td>31,800</td>
    </tr>
    <tr>
      <th>Agincourt South-Malvern West</th>
      <td>23,757</td>
      <td>31,825</td>
    </tr>
    <tr>
      <th>L'Amoreaux</th>
      <td>43,993</td>
      <td>31,826</td>
    </tr>
    <tr>
      <th>Elms-Old Rexdale</th>
      <td>9,456</td>
      <td>32,012</td>
    </tr>
    <tr>
      <th>Morningside</th>
      <td>17,455</td>
      <td>32,291</td>
    </tr>
    <tr>
      <th>Brookhaven-Amesbury</th>
      <td>17,757</td>
      <td>32,483</td>
    </tr>
    <tr>
      <th>North St. James Town</th>
      <td>18,615</td>
      <td>32,648</td>
    </tr>
    <tr>
      <th>Westminster-Branson</th>
      <td>26,274</td>
      <td>32,724</td>
    </tr>
    <tr>
      <th>Thistletown-Beaumond Heights</th>
      <td>10,360</td>
      <td>32,815</td>
    </tr>
    <tr>
      <th>Scarborough Village</th>
      <td>16,724</td>
      <td>32,913</td>
    </tr>
    <tr>
      <th>Weston</th>
      <td>17,992</td>
      <td>32,997</td>
    </tr>
    <tr>
      <th>Bendale</th>
      <td>29,960</td>
      <td>33,256</td>
    </tr>
    <tr>
      <th>Keelesdale-Eglinton West</th>
      <td>11,058</td>
      <td>33,316</td>
    </tr>
    <tr>
      <th>West Hill</th>
      <td>27,392</td>
      <td>33,323</td>
    </tr>
    <tr>
      <th>Weston-Pelham Park</th>
      <td>11,098</td>
      <td>33,528</td>
    </tr>
    <tr>
      <th>Beechborough-Greenbrook</th>
      <td>6,577</td>
      <td>33,829</td>
    </tr>
    <tr>
      <th>Rockcliffe-Smythe</th>
      <td>22,246</td>
      <td>34,059</td>
    </tr>
    <tr>
      <th>Downsview-Roding-CFB</th>
      <td>35,052</td>
      <td>34,168</td>
    </tr>
    <tr>
      <th>Tam O'Shanter-Sullivan</th>
      <td>27,446</td>
      <td>34,200</td>
    </tr>
    <tr>
      <th>Rexdale-Kipling</th>
      <td>10,529</td>
      <td>34,418</td>
    </tr>
    <tr>
      <th>Regent Park</th>
      <td>10,803</td>
      <td>34,597</td>
    </tr>
    <tr>
      <th>Briar Hill-Belgravia</th>
      <td>14,257</td>
      <td>34,768</td>
    </tr>
    <tr>
      <th>Newtonbrook West</th>
      <td>23,831</td>
      <td>34,904</td>
    </tr>
    <tr>
      <th>Wexford/Maryvale</th>
      <td>27,917</td>
      <td>35,047</td>
    </tr>
    <tr>
      <th>Caledonia-Fairbank</th>
      <td>9,955</td>
      <td>35,112</td>
    </tr>
    <tr>
      <th>South Parkdale</th>
      <td>21,849</td>
      <td>35,207</td>
    </tr>
    <tr>
      <th>Victoria Village</th>
      <td>17,510</td>
      <td>35,786</td>
    </tr>
    <tr>
      <th>Clairlea-Birchmount</th>
      <td>26,984</td>
      <td>36,232</td>
    </tr>
    <tr>
      <th>Pleasant View</th>
      <td>15,818</td>
      <td>36,346</td>
    </tr>
    <tr>
      <th>Henry Farm</th>
      <td>15,723</td>
      <td>36,359</td>
    </tr>
    <tr>
      <th>Kingsview Village-The Westway</th>
      <td>22,000</td>
      <td>36,674</td>
    </tr>
    <tr>
      <th>Maple Leaf</th>
      <td>10,111</td>
      <td>37,108</td>
    </tr>
    <tr>
      <th>Don Valley Village</th>
      <td>27,051</td>
      <td>37,379</td>
    </tr>
    <tr>
      <th>Kensington-Chinatown</th>
      <td>17,945</td>
      <td>37,422</td>
    </tr>
    <tr>
      <th>Etobicoke West Mall</th>
      <td>11,848</td>
      <td>38,255</td>
    </tr>
    <tr>
      <th>Yorkdale-Glen Park</th>
      <td>14,804</td>
      <td>38,527</td>
    </tr>
    <tr>
      <th>Pelmo Park-Humberlea</th>
      <td>10,722</td>
      <td>39,003</td>
    </tr>
    <tr>
      <th>Rouge</th>
      <td>46,496</td>
      <td>39,556</td>
    </tr>
    <tr>
      <th>Dovercourt-Wallace Emerson-Junction</th>
      <td>36,625</td>
      <td>39,740</td>
    </tr>
    <tr>
      <th>Oakwood Village</th>
      <td>21,210</td>
      <td>39,803</td>
    </tr>
    <tr>
      <th>Hillcrest Village</th>
      <td>16,934</td>
      <td>40,442</td>
    </tr>
    <tr>
      <th>Dufferin Grove</th>
      <td>11,785</td>
      <td>40,588</td>
    </tr>
    <tr>
      <th>Highland Creek</th>
      <td>12,494</td>
      <td>40,972</td>
    </tr>
    <tr>
      <th>Corso Italia-Davenport</th>
      <td>14,133</td>
      <td>41,717</td>
    </tr>
    <tr>
      <th>Parkwoods-Donalda</th>
      <td>34,805</td>
      <td>42,516</td>
    </tr>
    <tr>
      <th>O'Connor-Parkview</th>
      <td>18,675</td>
      <td>43,907</td>
    </tr>
    <tr>
      <th>New Toronto</th>
      <td>11,463</td>
      <td>44,101</td>
    </tr>
    <tr>
      <th>Willowridge-Martingrove-Richview</th>
      <td>22,156</td>
      <td>44,177</td>
    </tr>
    <tr>
      <th>Broadview North</th>
      <td>11,499</td>
      <td>44,557</td>
    </tr>
    <tr>
      <th>Willowdale West</th>
      <td>16,936</td>
      <td>44,576</td>
    </tr>
    <tr>
      <th>Cliffcrest</th>
      <td>15,935</td>
      <td>44,718</td>
    </tr>
    <tr>
      <th>Newtonbrook East</th>
      <td>16,097</td>
      <td>45,212</td>
    </tr>
    <tr>
      <th>Willowdale East</th>
      <td>50,434</td>
      <td>45,326</td>
    </tr>
    <tr>
      <th>Humber Heights-Westmount</th>
      <td>10,948</td>
      <td>45,615</td>
    </tr>
    <tr>
      <th>Little Portugal</th>
      <td>15,559</td>
      <td>45,737</td>
    </tr>
    <tr>
      <th>Bathurst Manor</th>
      <td>15,873</td>
      <td>45,936</td>
    </tr>
    <tr>
      <th>Greenwood-Coxwell</th>
      <td>14,417</td>
      <td>46,206</td>
    </tr>
    <tr>
      <th>Eringate-Centennial-West Deane</th>
      <td>18,588</td>
      <td>47,002</td>
    </tr>
    <tr>
      <th>Bayview Woods-Steeles</th>
      <td>13,154</td>
      <td>47,246</td>
    </tr>
    <tr>
      <th>Long Branch</th>
      <td>10,084</td>
      <td>47,384</td>
    </tr>
    <tr>
      <th>Alderwood</th>
      <td>12,054</td>
      <td>47,709</td>
    </tr>
    <tr>
      <th>Woodbine-Lumsden</th>
      <td>7,865</td>
      <td>47,710</td>
    </tr>
    <tr>
      <th>Englemount-Lawrence</th>
      <td>22,372</td>
      <td>48,134</td>
    </tr>
    <tr>
      <th>Blake-Jones</th>
      <td>7,727</td>
      <td>48,511</td>
    </tr>
    <tr>
      <th>Junction Area</th>
      <td>14,366</td>
      <td>49,709</td>
    </tr>
    <tr>
      <th>Roncesvalles</th>
      <td>14,974</td>
      <td>50,580</td>
    </tr>
    <tr>
      <th>Trinity-Bellwoods</th>
      <td>16,556</td>
      <td>50,694</td>
    </tr>
    <tr>
      <th>University</th>
      <td>7,607</td>
      <td>50,757</td>
    </tr>
    <tr>
      <th>Clanton Park</th>
      <td>16,472</td>
      <td>51,181</td>
    </tr>
    <tr>
      <th>Danforth East York</th>
      <td>17,180</td>
      <td>51,846</td>
    </tr>
    <tr>
      <th>Bayview Village</th>
      <td>21,396</td>
      <td>52,035</td>
    </tr>
    <tr>
      <th>Islington-City Centre West</th>
      <td>43,965</td>
      <td>52,787</td>
    </tr>
    <tr>
      <th>Guildwood</th>
      <td>9,917</td>
      <td>53,203</td>
    </tr>
    <tr>
      <th>Old East York</th>
      <td>9,233</td>
      <td>53,315</td>
    </tr>
    <tr>
      <th>Church-Yonge Corridor</th>
      <td>31,340</td>
      <td>53,583</td>
    </tr>
    <tr>
      <th>South Riverdale</th>
      <td>27,876</td>
      <td>53,803</td>
    </tr>
    <tr>
      <th>Centennial Scarborough</th>
      <td>13,362</td>
      <td>54,045</td>
    </tr>
    <tr>
      <th>East End-Danforth</th>
      <td>21,381</td>
      <td>54,324</td>
    </tr>
    <tr>
      <th>Birchcliffe-Cliffside</th>
      <td>22,291</td>
      <td>54,360</td>
    </tr>
    <tr>
      <th>Mimico (includes Humber Bay Shores)</th>
      <td>33,964</td>
      <td>54,438</td>
    </tr>
    <tr>
      <th>Wychwood</th>
      <td>14,349</td>
      <td>54,460</td>
    </tr>
    <tr>
      <th>Woodbine Corridor</th>
      <td>12,541</td>
      <td>55,199</td>
    </tr>
    <tr>
      <th>Danforth</th>
      <td>9,666</td>
      <td>55,225</td>
    </tr>
    <tr>
      <th>Bay Street Corridor</th>
      <td>25,797</td>
      <td>56,526</td>
    </tr>
    <tr>
      <th>Mount Pleasant West</th>
      <td>29,658</td>
      <td>57,039</td>
    </tr>
    <tr>
      <th>High Park North</th>
      <td>22,162</td>
      <td>57,465</td>
    </tr>
    <tr>
      <th>Palmerston-Little Italy</th>
      <td>13,826</td>
      <td>58,071</td>
    </tr>
    <tr>
      <th>Moss Park</th>
      <td>20,506</td>
      <td>58,915</td>
    </tr>
    <tr>
      <th>Markland Wood</th>
      <td>10,554</td>
      <td>62,378</td>
    </tr>
    <tr>
      <th>Cabbagetown-South St. James Town</th>
      <td>11,669</td>
      <td>63,012</td>
    </tr>
    <tr>
      <th>Stonegate-Queensway</th>
      <td>25,051</td>
      <td>64,140</td>
    </tr>
    <tr>
      <th>Humewood-Cedarvale</th>
      <td>14,365</td>
      <td>65,274</td>
    </tr>
    <tr>
      <th>Banbury-Don Mills</th>
      <td>27,695</td>
      <td>67,757</td>
    </tr>
    <tr>
      <th>Waterfront Communities-The Island</th>
      <td>65,913</td>
      <td>70,600</td>
    </tr>
    <tr>
      <th>Niagara</th>
      <td>31,180</td>
      <td>70,623</td>
    </tr>
    <tr>
      <th>Playter Estates-Danforth</th>
      <td>7,804</td>
      <td>70,831</td>
    </tr>
    <tr>
      <th>High Park-Swansea</th>
      <td>23,925</td>
      <td>71,204</td>
    </tr>
    <tr>
      <th>Runnymede-Bloor West Village</th>
      <td>10,070</td>
      <td>71,888</td>
    </tr>
    <tr>
      <th>Lansing-Westgate</th>
      <td>16,164</td>
      <td>72,371</td>
    </tr>
    <tr>
      <th>North Riverdale</th>
      <td>11,916</td>
      <td>73,253</td>
    </tr>
    <tr>
      <th>Lambton Baby Point</th>
      <td>7,985</td>
      <td>76,629</td>
    </tr>
    <tr>
      <th>Forest Hill North</th>
      <td>12,806</td>
      <td>85,099</td>
    </tr>
    <tr>
      <th>Mount Pleasant East</th>
      <td>16,775</td>
      <td>85,340</td>
    </tr>
    <tr>
      <th>Yonge-Eglinton</th>
      <td>11,817</td>
      <td>89,330</td>
    </tr>
    <tr>
      <th>The Beaches</th>
      <td>21,567</td>
      <td>92,580</td>
    </tr>
    <tr>
      <th>Princess-Rosethorn</th>
      <td>11,051</td>
      <td>99,055</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
