<script>
  let productType;
  let price = 150;
  let weight = 500;
  let shippingType = `easyShip`;
  let shippingRange = `national`;

  $: closingFees = 0;
  $: handlingFees = 0;
  $: bulky = weight < 12000 ? false : true;

  $: if (shippingRange === `local` && bulky) {
    if (weight <= 12000) handlingFees = 181;
    else if (weight > 12000) handlingFees = 181 + ((weight - 12000) / 1000) * 4;
  } else if (shippingRange === `local`) {
    if (weight > 0 && weight <= 500) handlingFees = 38;
    else if (weight >= 501 && weight <= 1000) handlingFees = 38 + 16;
    else if (weight > 1000 && weight <= 5000)
      handlingFees = 38 + 16 + ((weight - 1000) / 1000) * 13;
    else if (weight > 5000)
      handlingFees =
        38 +
        16 +
        (weight < 5000 ? (weight - 1000) / 1000 : 4) * 13 +
        ((weight - 5000) / 1000) * 10;
  } else if (shippingRange === `regional` && bulky) {
    if (weight <= 12000) handlingFees = 266;
    else if (weight > 12000) handlingFees = 266 + ((weight - 12000) / 1000) * 5;
  } else if (shippingRange === `regional`) {
    if (weight > 0 && weight <= 500) handlingFees = 48;
    else if (weight >= 501 && weight <= 1000) handlingFees = 48 + 21;
    else if (weight > 1000 && weight <= 5000)
      handlingFees = 48 + 21 + ((weight - 1000) / 1000) * 18;
    else if (weight > 5000)
      handlingFees =
        48 +
        21 +
        (weight < 5000 ? (weight - 1000) / 1000 : 4) * 18 +
        ((weight - 5000) / 1000) * 11;
  } else if (shippingRange === `national`) {
    if (weight > 0 && weight <= 500) handlingFees = 69;
    else if (weight >= 501 && weight <= 1000) handlingFees = 69 + 26;
    else if (weight > 1000 && weight <= 5000)
      handlingFees = 69 + 26 + ((weight - 1000) / 1000) * 24;
    else if (weight > 5000)
      handlingFees =
        69 +
        26 +
        (weight < 5000 ? (weight - 1000) / 1000 : 4) * 24 +
        ((weight - 5000) / 1000) * 15;
  }

  $: if (shippingType === `easyShip`) {
    if (price > 0 && price <= 250) closingFees = 5;
    else if (price >= 251 && price <= 500) closingFees = 8;
    else if (price >= 501 && price <= 1000) closingFees = 28;
    else if (price > 1000) closingFees = 50;
  } else if (shippingType === `selfShip`) {
    if (price > 0 && price <= 250) closingFees = 6;
    else if (price >= 251 && price <= 500) closingFees = 16;
    else if (price >= 501 && price <= 1000) closingFees = 32;
    else if (price > 1000) closingFees = 59;
  } else if (shippingType === `easyShipPrime`) {
    if (price > 0 && price <= 250) closingFees = 8;
    else if (price >= 251 && price <= 500) closingFees = 11;
    else if (price >= 501 && price <= 1000) closingFees = 25;
    else if (price > 1000) closingFees = 45;
  }

  const percentage = (rate, amount) => (rate * amount) / 100;
</script>

<main>
  <h1>Amazon Selling Fees Calculator</h1>

  <label for="productPrice">Product Price (in INR):</label>
  <input type="number" min="0" bind:value={price} />
  <label for="productWeight">Product Weight (in grams):</label>
  <input type="number" min="0" bind:value={weight} />
  <label for="bulky">Is Heavy & Bulky:</label>
  <input
    type="checkbox"
    bind:checked={bulky}
    disabled={shippingRange === `national`} />

  <div>
    <label for="shippingType">Select Shipping Type</label>
    <select name="type" id="shippingType" bind:value={shippingType}>
      <optgroup label="By Seller">
        <option value="easyShip">Easy Ship</option>
        <option value="selfShip">Self Ship</option>
        <option value="easyShipPrime">Easy Ship: Prime</option>
      </optgroup>
      <optgroup label="By Amazon">
        <option value="FBA">Fulfillment by Amazon</option>
      </optgroup>
    </select>

    <label for="shippingRange">Select Shipping Range</label>
    <select name="type" id="shippingRange" bind:value={shippingRange}>
      <option value="local">Local</option>
      <option value="regional">Regional</option>
      {#if !bulky}
        <option value="national">National</option>
      {/if}
    </select>
  </div>

  <div>
    <label for="productCategory">Select Product Category</label>
    <select name="productCategory" bind:value={productType}>
      <option value="media">Media & Entertainment</option>
      <option value="beauty">
        Beauty, Baby, Toys, Grocery, Health & Personal Care
      </option>
      <option value="fashion">
        Fashion: Clothing, Accessories & Jewellery
      </option>
      <option value="kitchen">
        Home, Kitchen,Outdoor,Industrial Equipment, Sports, Collectibles & Face
        Masks
      </option>
      <option value="electronics">
        Electronics, Accessories & Musical Instruments
      </option>
    </select>
  </div>
  <div class="wrapper">
    <table>
      <thead>
        <tr>
          <th>Category</th>
          <th>Referral Fees</th>
          <th>Closing Fees</th>
          <th>Weight Handling Fees</th>
          <th>Total</th>
          <th>+18% GST</th>
          <th>List Price</th>
        </tr>
      </thead>
      <tbody>
        {#if productType === 'media'}
          <tr>
            <td>Movies</td>
            <td>{percentage(6.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(6.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(6.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(6.5, price) + percentage(18, closingFees + handlingFees + percentage(6.5, price))}
            </td>
          </tr>

          <tr>
            <td>Books</td>
            <td>
              {price <= 250 ? percentage(2, price) : price > 250 && price <= 500 ? percentage(4, price) : price > 500 && price <= 1000 ? percentage(9, price) : percentage(13, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 250 ? percentage(2, price) : price > 250 && price <= 500 ? percentage(4, price) : price > 500 && price <= 1000 ? percentage(9, price) : percentage(13, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 250 ? percentage(2, price) : price > 250 && price <= 500 ? percentage(4, price) : price > 500 && price <= 1000 ? percentage(9, price) : percentage(13, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(18, closingFees + handlingFees + (price <= 250 ? percentage(2, price) : price > 250 && price <= 500 ? percentage(4, price) : price > 500 && price <= 1000 ? percentage(9, price) : percentage(13, price))) + (price <= 250 ? percentage(2, price) : price > 250 && price <= 500 ? percentage(4, price) : price > 500 && price <= 1000 ? percentage(9, price) : percentage(13, price))}
            </td>
          </tr>
          <tr>
            <td>Music</td>
            <td>{percentage(6.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(6.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(6.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(6.5, price) + percentage(18, closingFees + handlingFees + percentage(6.5, price))}
            </td>
          </tr>
          <tr>
            <td>Video Games</td>
            <td>{percentage(7, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(7, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(7, price) + percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
          </tr>
          <tr>
            <td>Software Products</td>
            <td>{percentage(11.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(11.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(11.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(11.5, price) + percentage(18, closingFees + handlingFees + percentage(11.5, price))}
            </td>
          </tr>
          <tr>
            <td>Video Games: Consoles</td>
            <td>{percentage(7, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(7, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(7, price) + percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
          </tr>
          <tr>
            <td>Video Games: Accessories</td>
            <td>
              {price <= 500 ? percentage(9, price) : percentage(12, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 500 ? percentage(9, price) : percentage(12, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 500 ? percentage(9, price) : percentage(12, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 500 ? percentage(9, price) : percentage(12, price)) + percentage(18, closingFees + handlingFees + (price <= 500 ? percentage(9, price) : percentage(12, price)))}
            </td>
          </tr>
          <tr>
            <td>Video Games: Online Game Services</td>
            <td>{percentage(2, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(2, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(2, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(2, price) + percentage(18, closingFees + handlingFees + percentage(2, price))}
            </td>
          </tr>
        {:else if productType === 'beauty'}
          <tr>
            <td>Pet Products</td>
            <td>
              {price <= 250 ? percentage(6.5, price) : percentage(11, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 250 ? percentage(6.5, price) : percentage(11, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 250 ? percentage(6.5, price) : percentage(11, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 250 ? percentage(6.5, price) : percentage(11, price)) + percentage(18, closingFees + handlingFees + (price <= 250 ? percentage(6.5, price) : percentage(11, price)))}
            </td>
          </tr>
          <tr>
            <td>Beauty Products</td>
            <td>{percentage(4.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(4.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(4.5, price) + percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
          </tr>
          <tr>
            <td>Beauty: Fragrance</td>
            <td>
              {price <= 250 ? percentage(7.5, price) : percentage(12.5, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 250 ? percentage(7.5, price) : percentage(12.5, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 250 ? percentage(7.5, price) : percentage(12.5, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 250 ? percentage(7.5, price) : percentage(12.5, price)) + percentage(18, closingFees + handlingFees + (price <= 250 ? percentage(7.5, price) : percentage(12.5, price)))}
            </td>
          </tr>
          <tr>
            <td>Luxury Beauty</td>
            <td>{percentage(4.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(4.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(4.5, price) + percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
          </tr>
          <tr>
            <td>Health & Personal Care (HPC): Other Subcategories</td>
            <td>{percentage(11, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(11, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(11, price) + percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
          </tr>
          <tr>
            <td>HPC - Medical Equipment & Contact Lenses</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>HPC - Nutrition</td>
            <td>{percentage(9, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(9, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(9, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(9, price) + percentage(18, closingFees + handlingFees + percentage(9, price))}
            </td>
          </tr>
          <tr>
            <td>HPC - Household Supplies, Personl Care & Ayurveda</td>
            <td>{percentage(4.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(4.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(4.5, price) + percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
          </tr>
          <tr>
            <td>Personal Care Appliances (Grooming & Styling)</td>
            <td>{percentage(9.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(9.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(9.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(9.5, price) + percentage(18, closingFees + handlingFees + percentage(9.5, price))}
            </td>
          </tr>
          <tr>
            <td>Personal Care Appliances (excluding Grooming & Styling)</td>
            <td>{percentage(7.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(7.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(7.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(7.5, price) + percentage(18, closingFees + handlingFees + percentage(7.5, price))}
            </td>
          </tr>
          <tr>
            <td>Personal Care Appliances: Electric Massagers</td>
            <td>{percentage(9, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(9, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(9, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(9, price) + percentage(18, closingFees + handlingFees + percentage(9, price))}
            </td>
          </tr>
          <tr>
            <td>Baby Products</td>
            <td>{percentage(6, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(6, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(6, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(6, price) + percentage(18, closingFees + handlingFees + percentage(6, price))}
            </td>
          </tr>
          <tr>
            <td>
              Baby Hardlines: Swings, Bouncers & Rockers, Carriers and Walkers
            </td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Baby Safety: Guards & Locks</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Baby Room Decor</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Baby Furniture</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Baby Car Seats & Accessories</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Baby Strollers, Buggies & Prams</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Grocery & Gourmet</td>
            <td>{percentage(4.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(4.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(4.5, price) + percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
          </tr>
          <tr>
            <td>Prescription Medicine</td>
            <td>{percentage(4.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(4.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(4.5, price) + percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
          </tr>
          <tr>
            <td>Toys</td>
            <td>{percentage(9.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(9.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(9.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(9.5, price) + percentage(18, closingFees + handlingFees + percentage(9.5, price))}
            </td>
          </tr>
          <tr>
            <td>Toys: Drones</td>
            <td>{percentage(10.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(10.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(10.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(10.5, price) + percentage(18, closingFees + handlingFees + percentage(10.5, price))}
            </td>
          </tr>
        {:else if productType === 'fashion'}
          <tr>
            <td>Apparel</td>
            <td>
              {price <= 300 ? percentage(13, price) : percentage(17, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price)) + percentage(18, closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price)))}
            </td>
          </tr>
          <tr>
            <td>Apparel: Sarees & Dress Material</td>
            <td>
              {price <= 500 ? percentage(13.5, price) : price > 500 && price <= 1000 ? percentage(17, price) : percentage(19, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 500 ? percentage(13.5, price) : price > 500 && price <= 1000 ? percentage(17, price) : percentage(19, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 500 ? percentage(13.5, price) : price > 500 && price <= 1000 ? percentage(17, price) : percentage(19, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 500 ? percentage(13.5, price) : price > 500 && price <= 1000 ? percentage(17, price) : percentage(19, price)) + percentage(18, closingFees + handlingFees + (price <= 500 ? percentage(13.5, price) : price > 500 && price <= 1000 ? percentage(17, price) : percentage(19, price)))}
            </td>
          </tr>
          <tr>
            <td>
              Apparel: Men's T-Shirt (except Polos, Tank Tops & Full Sleeve
              Tops)
            </td>
            <td>{percentage(15.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(15.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(15.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(15.5, price) + percentage(18, closingFees + handlingFees + percentage(15.5, price))}
            </td>
          </tr>
          <tr>
            <td>Apparel: Accessories</td>
            <td>{percentage(17, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(17, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(17, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(17, price) + percentage(18, closingFees + handlingFees + percentage(17, price))}
            </td>
          </tr>
          <tr>
            <td>Apparel: Innerware</td>
            <td>{percentage(13, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(13, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(13, price) + percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
          </tr>
          <tr>
            <td>Apparel: Sleepware</td>
            <td>{percentage(13, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(13, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(13, price) + percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
          </tr>
          <tr>
            <td>Eyeware</td>
            <td>{percentage(10, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(10, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(10, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(10, price) + percentage(18, closingFees + handlingFees + percentage(10, price))}
            </td>
          </tr>
          <tr>
            <td>Watches</td>
            <td>{percentage(13.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(13.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(13.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(13.5, price) + percentage(18, closingFees + handlingFees + percentage(13.5, price))}
            </td>
          </tr>
          <tr>
            <td>Shoes</td>
            <td>{percentage(14, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(14, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(14, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(14, price) + percentage(18, closingFees + handlingFees + percentage(14, price))}
            </td>
          </tr>
          <tr>
            <td>Flip-Flops, Fashion Sandals & Slippers</td>
            <td>{percentage(10.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(10.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(10.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(10.5, price) + percentage(18, closingFees + handlingFees + percentage(10.5, price))}
            </td>
          </tr>
          <tr>
            <td>Shoes: Kids Footwear</td>
            <td>{percentage(10.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(10.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(10.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(10.5, price) + percentage(18, closingFees + handlingFees + percentage(10.5, price))}
            </td>
          </tr>
          <tr>
            <td>Handbags</td>
            <td>{percentage(11, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(11, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(11, price) + percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
          </tr>
          <tr>
            <td>Wallets</td>
            <td>{percentage(11.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(11.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(11.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(11.5, price) + percentage(18, closingFees + handlingFees + percentage(11.5, price))}
            </td>
          </tr>
          <tr>
            <td>Backpacks</td>
            <td>{percentage(10, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(10, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(10, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(10, price) + percentage(18, closingFees + handlingFees + percentage(10, price))}
            </td>
          </tr>
          <tr>
            <td>Luggage: Suitcase & Trolleys</td>
            <td>{percentage(6, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(6, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(6, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(6, price) + percentage(18, closingFees + handlingFees + percentage(6, price))}
            </td>
          </tr>
          <tr>
            <td>Luggage: Travel Accessories</td>
            <td>{percentage(11, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(11, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(11, price) + percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
          </tr>
          <tr>
            <td>Luggage: Other Subcategories</td>
            <td>{percentage(5.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5.5, price) + percentage(18, closingFees + handlingFees + percentage(5.5, price))}
            </td>
          </tr>
          <tr>
            <td>Fashion Jewellery</td>
            <td>{percentage(22.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(22.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(22.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(22.5, price) + percentage(18, closingFees + handlingFees + percentage(22.5, price))}
            </td>
          </tr>
          <tr>
            <td>Silver Jewellery</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Silver Coins & Bars</td>
            <td>{percentage(2.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {price + closingFees + handlingFees + percentage(2.5, price)}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(2.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(2.5, price) + percentage(18, closingFees + handlingFees + percentage(2.5, price))}
            </td>
          </tr>
          <tr>
            <td>Fine Jewellery: Unstudded & Solitaire</td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
          <tr>
            <td>Fine Jewellery: Studded</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Fine Jewellery: Gold Coins</td>
            <td>{percentage(2.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(2.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(2.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(2.5, price) + percentage(18, closingFees + handlingFees + percentage(2.5, price))}
            </td>
          </tr>
        {:else if productType === 'kitchen'}
          <tr>
            <td>Kitchen: Non Appliances</td>
            <td>
              {price <= 300 ? percentage(5, price) : percentage(11.5, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 300 ? percentage(5, price) : percentage(11.5, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 300 ? percentage(5, price) : percentage(11.5, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 300 ? percentage(5, price) : percentage(11.5, price)) + percentage(18, closingFees + handlingFees + (price <= 300 ? percentage(5, price) : percentage(11.5, price)))}
            </td>
          </tr>
          <tr>
            <td>Kitchen: Gas Stoves & Pressure Cookers</td>
            <td>{percentage(7, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(7, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(7, price) + percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
          </tr>
          <tr>
            <td>Kitchen: Glassware & Ceramicware</td>
            <td>{percentage(11, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {price + closingFees + handlingFees + percentage(11, price)}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(11, price) + percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
          </tr>
          <tr>
            <td>Small Appliances</td>
            <td>
              {price <= 5000 ? percentage(5.5, price) : percentage(6.5, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 5000 ? percentage(5.5, price) : percentage(6.5, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 5000 ? percentage(5.5, price) : percentage(6.5, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 5000 ? percentage(5.5, price) : percentage(6.5, price)) + percentage(18, closingFees + handlingFees + (price <= 5000 ? percentage(5.5, price) : percentage(6.5, price)))}
            </td>
          </tr>
          <tr>
            <td>
              Wall Art (Paintings, Posters, Decorative Stickers & Art Prints)
            </td>
            <td>{percentage(13.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(13.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(13.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(13.5, price) + percentage(18, closingFees + handlingFees + percentage(13.5, price))}
            </td>
          </tr>
          <tr>
            <td>Home: Fragrance & Candles</td>
            <td>{percentage(10.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(10.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(10.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(10.5, price) + percentage(18, closingFees + handlingFees + percentage(10.5, price))}
            </td>
          </tr>
          <tr>
            <td>Home Furnishing</td>
            <td>{percentage(12, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(12, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(12, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(12, price) + percentage(18, closingFees + handlingFees + percentage(12, price))}
            </td>
          </tr>
          <tr>
            <td>Home: Carpets, Bedsheets, Blankets & Covers</td>
            <td>{percentage(9, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(9, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(9, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(9, price) + percentage(18, closingFees + handlingFees + percentage(9, price))}
            </td>
          </tr>
          <tr>
            <td>Home Improvement Accessories</td>
            <td>
              {price <= 250 ? percentage(6.5, price) : percentage(11.5, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 250 ? percentage(6.5, price) : percentage(11.5, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 250 ? percentage(6.5, price) : percentage(11.5, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 250 ? percentage(6.5, price) : percentage(11.5, price)) + percentage(18, closingFees + handlingFees + (price <= 250 ? percentage(6.5, price) : percentage(11.5, price)))}
            </td>
          </tr>
          <tr>
            <td>Home Improvement: Excluding Accessories</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>
              Home Improvement: Ladders, Kitchen, Bath Fixtures & Home Security
              Systems
            </td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Home Storage</td>
            <td>{percentage(13, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(13, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(13, price) + percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
          </tr>
          <tr>
            <td>Home: Other Subcategories</td>
            <td>{percentage(17, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(17, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(17, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(17, price) + percentage(18, closingFees + handlingFees + percentage(17, price))}
            </td>
          </tr>
          <tr>
            <td>Home: Waste & Recycling</td>
            <td>{percentage(6, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(6, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(6, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(6, price) + percentage(18, closingFees + handlingFees + percentage(6, price))}
            </td>
          </tr>
          <tr>
            <td>Craft Materials</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Lawn & Garden: Solar Devices</td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
          <tr>
            <td>Lawn & Garden: Chemical Pest Control</td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
          <tr>
            <td>
              Outdoor Equipments: Pumps, Generators, Grilling, Agriculture
              Machinery, Saws, Tilers, Pressure Washers, Lawn Mowers & String
              Trimmers
            </td>
            <td>{percentage(5.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5.5, price) + percentage(18, closingFees + handlingFees + percentage(5.5, price))}
            </td>
          </tr>
          <tr>
            <td>Lawn & Garden: Other Subcategories</td>
            <td>
              {price <= 15000 ? percentage(10, price) : percentage(5, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 15000 ? percentage(10, price) : percentage(5, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 15000 ? percentage(10, price) : percentage(5, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 15000 ? percentage(10, price) : percentage(5, price)) + percentage(18, closingFees + handlingFees + (price <= 15000 ? percentage(10, price) : percentage(5, price)))}
            </td>
          </tr>
          <tr>
            <td>Lawn & Garden: Plants, Seeds & Bulbs</td>
            <td>
              {price <= 300 ? percentage(7, price) : percentage(10, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {price + closingFees + handlingFees + (price <= 300 ? percentage(7, price) : percentage(10, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 300 ? percentage(7, price) : percentage(10, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 300 ? percentage(7, price) : percentage(10, price)) + percentage(18, closingFees + handlingFees + (price <= 300 ? percentage(7, price) : percentage(10, price)))}
            </td>
          </tr>
          <tr>
            <td>
              Indoor Lighting: Wall, Ceiling Fixture Lights, Lamp Bases, Lamp
              Shades
            </td>
            <td>{percentage(13.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(13.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(13.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(13.5, price) + percentage(18, closingFees + handlingFees + percentage(13.5, price))}
            </td>
          </tr>
          <tr>
            <td>LED Bulbs & Patterns</td>
            <td>{percentage(7, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(7, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(7, price) + percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
          </tr>
          <tr>
            <td>Indoor Lighting: Others</td>
            <td>{percentage(16, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(16, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(16, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(16, price) + percentage(18, closingFees + handlingFees + percentage(16, price))}
            </td>
          </tr>
          <tr>
            <td>Clocks</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Automative: Other Subcategories</td>
            <td>{percentage(20, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(20, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(20, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(20, price) + percentage(18, closingFees + handlingFees + percentage(20, price))}
            </td>
          </tr>
          <tr>
            <td>Automative: Tyres & Rims</td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
          <tr>
            <td>
              Automative: Lubricants, Car Parts & Helmets, Vehicle Care & Tools
            </td>
            <td>{percentage(6.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(6.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(6.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(6.5, price) + percentage(18, closingFees + handlingFees + percentage(6.5, price))}
            </td>
          </tr>
          <tr>
            <td>Automative: Accessories</td>
            <td>{percentage(13, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(13, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(13, price) + percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
          </tr>
          <tr>
            <td>Automative: Vehicles</td>
            <td>{percentage(2, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(2, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(2, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(2, price) + percentage(18, closingFees + handlingFees + percentage(2, price))}
            </td>
          </tr>
          <tr>
            <td>Large Appliances: Accessories</td>
            <td>{percentage(13, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(13, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(13, price) + percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
          </tr>
          <tr>
            <td>Large Appliances: Chimneys</td>
            <td>{percentage(7, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(7, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(7, price) + percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
          </tr>
          <tr>
            <td>Large Appliances: Excluding Accessories & Chimneys</td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
          <tr>
            <td>Large Appliances: Regrigerators</td>
            <td>{percentage(4.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(4.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(4.5, price) + percentage(18, closingFees + handlingFees + percentage(4.5, price))}
            </td>
          </tr>
          <tr>
            <td>Furniture</td>
            <td>
              {price <= 15000 ? percentage(14, price) : percentage(10, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 15000 ? percentage(14, price) : percentage(10, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 15000 ? percentage(14, price) : percentage(10, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 15000 ? percentage(14, price) : percentage(10, price)) + percentage(18, closingFees + handlingFees + (price <= 15000 ? percentage(14, price) : percentage(10, price)))}
            </td>
          </tr>
          <tr>
            <td>Bean Bags & Inflatables</td>
            <td>{percentage(10, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(10, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(10, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(10, price) + percentage(18, closingFees + handlingFees + percentage(10, price))}
            </td>
          </tr>
          <tr>
            <td>BISS Equipment: Lab Supplies & Fastners</td>
            <td>
              {price <= 15000 ? percentage(11.5, price) : percentage(5, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 15000 ? percentage(11.5, price) : percentage(5, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 15000 ? percentage(11.5, price) : percentage(5, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 15000 ? percentage(11.5, price) : percentage(5, price)) + percentage(18, closingFees + handlingFees + (price <= 15000 ? percentage(11.5, price) : percentage(5, price)))}
            </td>
          </tr>
          <tr>
            <td>Power Tools, Measuring Instruments, Tapes & Adhesives</td>
            <td>
              {price <= 15000 ? percentage(8, price) : percentage(5, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 15000 ? percentage(8, price) : percentage(5, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 15000 ? percentage(8, price) : percentage(5, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 15000 ? percentage(8, price) : percentage(5, price)) + percentage(18, closingFees + handlingFees + (price <= 15000 ? percentage(8, price) : percentage(5, price)))}
            </td>
          </tr>
          <tr>
            <td>
              Food Equipment, Material Handling, Professional Medicine, Health &
              Cleaning Supplies
            </td>
            <td>{percentage(5.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5.5, price) + percentage(18, closingFees + handlingFees + percentage(5.5, price))}
            </td>
          </tr>
          <tr>
            <td>Bicycles</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Gym Equipments</td>
            <td>{percentage(9, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(9, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(9, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(9, price) + percentage(18, closingFees + handlingFees + percentage(9, price))}
            </td>
          </tr>
          <tr>
            <td>
              Sports: Cricket & Badminton Equipments, Tennis, Table Tennis,
              Squash, Football, Volleyball, Basketball, Throwball, Swimming
            </td>
            <td>{percentage(6, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(6, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(6, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(6, price) + percentage(18, closingFees + handlingFees + percentage(6, price))}
            </td>
          </tr>
          <tr>
            <td>Sports: Others</td>
            <td>
              {price <= 250 ? percentage(9, price) : percentage(11.5, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 250 ? percentage(9, price) : percentage(11.5, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 250 ? percentage(9, price) : percentage(11.5, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 250 ? percentage(9, price) : percentage(11.5, price)) + percentage(18, closingFees + handlingFees + (price <= 250 ? percentage(9, price) : percentage(11.5, price)))}
            </td>
          </tr>
          <tr>
            <td>Consumable Physical Gift Card</td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
          <tr>
            <td>Sports Collectibles</td>
            <td>
              {price <= 300 ? percentage(13, price) : percentage(17, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price)) + percentage(18, closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price)))}
            </td>
          </tr>
          <tr>
            <td>Entertainment Collectibles</td>
            <td>
              {price <= 300 ? percentage(13, price) : percentage(17, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price)) + percentage(18, closingFees + handlingFees + (price <= 300 ? percentage(13, price) : percentage(17, price)))}
            </td>
          </tr>
          <tr>
            <td>Coin Collectibles</td>
            <td>{percentage(15, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(15, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(15, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(15, price) + percentage(18, closingFees + handlingFees + percentage(15, price))}
            </td>
          </tr>
          <tr>
            <td>Fine Art</td>
            <td>{percentage(20, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(20, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(20, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(20, price) + percentage(18, closingFees + handlingFees + percentage(20, price))}
            </td>
          </tr>
          <tr>
            <td>
              Masks, Gloves, Personal Protective Equipment & Medical Apparel
            </td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
        {:else if productType === 'electronics'}
          <tr>
            <td>Mobile Phones & Tablets including Graphic Tablets</td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
          <tr>
            <td>Laptops</td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
          <tr>
            <td>Scanners & Printers</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>PC Components: RAM, Motherboards</td>
            <td>{percentage(7, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(7, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(7, price) + percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
          </tr>
          <tr>
            <td>Desktops</td>
            <td>{percentage(6.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(6.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(6.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(6.5, price) + percentage(18, closingFees + handlingFees + percentage(6.5, price))}
            </td>
          </tr>
          <tr>
            <td>Monitors</td>
            <td>{percentage(6.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(6.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(6.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(6.5, price) + percentage(18, closingFees + handlingFees + percentage(6.5, price))}
            </td>
          </tr>
          <tr>
            <td>Laptop Battery</td>
            <td>{percentage(14, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(14, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(14, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(14, price) + percentage(18, closingFees + handlingFees + percentage(14, price))}
            </td>
          </tr>
          <tr>
            <td>Laptop Bags & Sleeves</td>
            <td>{percentage(10, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(10, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(10, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(10, price) + percentage(18, closingFees + handlingFees + percentage(10, price))}
            </td>
          </tr>
          <tr>
            <td>USB Flash/Pen Drives</td>
            <td>{percentage(16, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(16, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(16, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(16, price) + percentage(18, closingFees + handlingFees + percentage(16, price))}
            </td>
          </tr>
          <tr>
            <td>Hard Disks</td>
            <td>{percentage(8.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8.5, price) + percentage(18, closingFees + handlingFees + percentage(8.5, price))}
            </td>
          </tr>
          <tr>
            <td>Kindle Accessories</td>
            <td>{percentage(25, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(25, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(25, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(25, price) + percentage(18, closingFees + handlingFees + percentage(25, price))}
            </td>
          </tr>
          <tr>
            <td>Memory Cards</td>
            <td>{percentage(12, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(12, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(12, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(12, price) + percentage(18, closingFees + handlingFees + percentage(12, price))}
            </td>
          </tr>
          <tr>
            <td>Modems & Networking Devices</td>
            <td>{percentage(14, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(14, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(14, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(14, price) + percentage(18, closingFees + handlingFees + percentage(14, price))}
            </td>
          </tr>
          <tr>
            <td>Car Electronics Devices</td>
            <td>{percentage(5.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5.5, price) + percentage(18, closingFees + handlingFees + percentage(5.5, price))}
            </td>
          </tr>
          <tr>
            <td>
              Electronic Devices except TV, Camera & Camcorder, Camera Lenses &
              Accessories
            </td>
            <td>{percentage(8.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8.5, price) + percentage(18, closingFees + handlingFees + percentage(8.5, price))}
            </td>
          </tr>
          <tr>
            <td>Landline Phones</td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
          <tr>
            <td>Smart Watches & Accessories</td>
            <td>{percentage(14.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(14.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(14.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(14.5, price) + percentage(18, closingFees + handlingFees + percentage(14.5, price))}
            </td>
          </tr>
          <tr>
            <td>Television</td>
            <td>{percentage(6, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(6, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(6, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(6, price) + percentage(18, closingFees + handlingFees + percentage(6, price))}
            </td>
          </tr>
          <tr>
            <td>Camera & Camcorder</td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
          <tr>
            <td>Camera Lenses</td>
            <td>{percentage(7, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(7, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(7, price) + percentage(18, closingFees + handlingFees + percentage(7, price))}
            </td>
          </tr>
          <tr>
            <td>Camera Accessories</td>
            <td>{percentage(11, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(11, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(11, price) + percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
          </tr>
          <tr>
            <td>GPS Devices</td>
            <td>{percentage(13.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(13.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(13.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(13.5, price) + percentage(18, closingFees + handlingFees + percentage(13.5, price))}
            </td>
          </tr>
          <tr>
            <td>Speakers</td>
            <td>{percentage(11, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(11, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(11, price) + percentage(18, closingFees + handlingFees + percentage(11, price))}
            </td>
          </tr>
          <tr>
            <td>Headsets, Headphones & Earphones</td>
            <td>{percentage(15, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(15, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(15, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(15, price) + percentage(18, closingFees + handlingFees + percentage(15, price))}
            </td>
          </tr>
          <tr>
            <td>Keyboards & Mouse</td>
            <td>{percentage(13, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(13, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(13, price) + percentage(18, closingFees + handlingFees + percentage(13, price))}
            </td>
          </tr>
          <tr>
            <td>Power Banks</td>
            <td>{percentage(18, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(18, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(18, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(18, price) + percentage(18, closingFees + handlingFees + percentage(18, price))}
            </td>
          </tr>
          <tr>
            <td>Accessories: Electronics, PC & Wireless</td>
            <td>{percentage(17, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(17, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(17, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(17, price) + percentage(18, closingFees + handlingFees + percentage(17, price))}
            </td>
          </tr>
          <tr>
            <td>Cases, Cover, Skin, Screen Guard</td>
            <td>
              {price <= 150 ? percentage(3, price) : price > 150 && price <= 300 ? percentage(18, price) : price > 300 && price <= 500 ? percentage(20, price) : percentage(25, price)}
            </td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>
              {closingFees + handlingFees + (price <= 150 ? percentage(3, price) : price > 150 && price <= 300 ? percentage(18, price) : price > 300 && price <= 500 ? percentage(20, price) : percentage(25, price))}
            </td>
            <td>
              {percentage(18, closingFees + handlingFees + (price <= 150 ? percentage(3, price) : price > 150 && price <= 300 ? percentage(18, price) : price > 300 && price <= 500 ? percentage(20, price) : percentage(25, price)))}
            </td>
            <td>
              {price + closingFees + handlingFees + (price <= 150 ? percentage(3, price) : price > 150 && price <= 300 ? percentage(18, price) : price > 300 && price <= 500 ? percentage(20, price) : percentage(25, price)) + percentage(18, closingFees + handlingFees + (price <= 150 ? percentage(3, price) : price > 150 && price <= 300 ? percentage(18, price) : price > 300 && price <= 500 ? percentage(20, price) : percentage(25, price)))}
            </td>
          </tr>
          <tr>
            <td>Cables: Electronics, PC, Wireless</td>
            <td>{percentage(20, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(20, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(20, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(20, price) + percentage(18, closingFees + handlingFees + percentage(20, price))}
            </td>
          </tr>
          <tr>
            <td>Car Cradles, Lens Kits & Folio Cases</td>
            <td>{percentage(21, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(21, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(21, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(21, price) + percentage(18, closingFees + handlingFees + percentage(21, price))}
            </td>
          </tr>
          <tr>
            <td>Warranty Services</td>
            <td>{percentage(38, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(38, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(38, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(38, price) + percentage(18, closingFees + handlingFees + percentage(38, price))}
            </td>
          </tr>
          <tr>
            <td>Office Products</td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
          <tr>
            <td>Office Electronics Products</td>
            <td>{percentage(9, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(9, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(9, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(9, price) + percentage(18, closingFees + handlingFees + percentage(9, price))}
            </td>
          </tr>
          <tr>
            <td>Musical Instruments: Guitar</td>
            <td>{percentage(7.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(7.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(7.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(7.5, price) + percentage(18, closingFees + handlingFees + percentage(7.5, price))}
            </td>
          </tr>
          <tr>
            <td>Musical Instruments: Keyboard</td>
            <td>{percentage(5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(5, price) + percentage(18, closingFees + handlingFees + percentage(5, price))}
            </td>
          </tr>
          <tr>
            <td>Musical Instruments excluding Guitars & Keyboards</td>
            <td>{percentage(7.5, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(7.5, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(7.5, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(7.5, price) + percentage(18, closingFees + handlingFees + percentage(7.5, price))}
            </td>
          </tr>
          <tr>
            <td>
              Musical Instruments: DJ & VJ Equipment, Recording & Computer,
              Cables & Leads, Microphones, PA & Stage
            </td>
            <td>{percentage(8, price)}</td>
            <td>{closingFees}</td>
            <td>{handlingFees}</td>
            <td>{closingFees + handlingFees + percentage(8, price)}</td>
            <td>
              {percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
            <td>
              {price + closingFees + handlingFees + percentage(8, price) + percentage(18, closingFees + handlingFees + percentage(8, price))}
            </td>
          </tr>
        {/if}
      </tbody>
    </table>
  </div>
  <h6>
    For more details click
    <a
      href="https://services.amazon.in/services/sell-on-amazon/pricing.html"
      target="_blank"
      alt="Amazon">
      here.
    </a>
  </h6>
</main>
