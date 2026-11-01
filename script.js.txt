document.addEventListener('DOMContentLoaded', function() {
  const galleryImages = document.querySelectorAll('.gallery-img');
  const lightbox = document.getElementById('lightbox');
  const lightboxImg = document.getElementById('lightbox-img');
  if (galleryImages.length && lightbox) {
    galleryImages.forEach(img => {
      img.addEventListener('click', () => {
        lightboxImg.src = img.src;
        lightboxImg.alt = img.alt;
        lightbox.style.display = 'flex';
      });
    });
    lightbox.addEventListener('click', () => {
      lightbox.style.display = 'none';
    });
  }
  document.querySelectorAll('.accordion-btn').forEach(btn => {
    btn.addEventListener('click', () => {
      let content = btn.nextElementSibling;
      content.style.display = content.style.display === 'block' ? 'none' : 'block';
    });
  });
  const searchInput = document.getElementById('searchInput');
  if (searchInput) {
    searchInput.addEventListener('input', (e) => {
      let term = e.target.value.toLowerCase();
      document.querySelectorAll('.cupcake-card').forEach(card => {
        card.style.display = card.textContent.toLowerCase().includes(term) ? 'block' : 'none';
      });
    });
  }
  const sortSelect = document.getElementById('sortSelect');
  if (sortSelect) {
    sortSelect.addEventListener('change', (e) => {
      let container = document.querySelector('.grid-container');
      let cards = Array.from(container.querySelectorAll('.cupcake-card'));
      if (e.target.value === 'price-low') {
        cards.sort((a,b) => a.dataset.price - b.dataset.price);
      } else if (e.target.value === 'price-high') {
        cards.sort((a,b) => b.dataset.price - a.dataset.price);
      }
      cards.forEach(c => container.appendChild(c));
    });
  }
  const enquiryForm = document.getElementById('enquiryForm');
  if (enquiryForm) {
    enquiryForm.addEventListener('submit', function(e) {
      e.preventDefault();
      let name = document.getElementById('ename').value.trim();
      let email = document.getElementById('eemail').value.trim();
      let phone = document.getElementById('ephone').value.trim();
      let qty = parseInt(document.getElementById('equantity').value);
      let resultDiv = document.getElementById('enquiryResult');
      let errors = [];
      if (name.length < 2) { errors.push('Name must be at least 2 characters'); }
      if (!/^\S+@\S+\.\S+$/.test(email)) { errors.push('Enter a valid email'); }
      if (!/^[0-9]{10}$/.test(phone)) { errors.push('Phone must be 10 digits'); }
      if (isNaN(qty) || qty < 6 || qty > 500) { errors.push('Quantity must be between 6 and 500'); }
      if (errors.length > 0) {
        resultDiv.innerHTML = '<p style="color:red;">' + errors.join('<br>') + '</p>';
        return;
      }
      let total = qty * 25;
      resultDiv.innerHTML = '<h3>Thank you, ' + name + '</h3><p>Your enquiry for ' + qty + ' cupcakes has been received.</p><p><strong>Estimated Cost: R' + total + '</strong></p><p>Availability: We can fulfill this order within 3 working days. We will email you at ' + email + ' to confirm.</p>';
      fetch('https://formspree.io/f/your-form-id', {
        method: 'POST',
        body: new FormData(enquiryForm),
        headers: { 'Accept': 'application/json' }
      }).then(() => console.log('Enquiry sent via AJAX')).catch(() => console.log('AJAX fallback'));
    });
  }
  const contactForm = document.getElementById('contactForm');
  if (contactForm) {
    contactForm.addEventListener('submit', function(e) {
      e.preventDefault();
      let cname = document.getElementById('cname').value.trim();
      let cemail = document.getElementById('cemail').value.trim();
      let ctype = document.getElementById('ctype').value;
      let cmsg = document.getElementById('cmessage').value.trim();
      let resultDiv = document.getElementById('contactResult');
      if (cname.length < 2 || !/^\S+@\S+\.\S+$/.test(cemail) || cmsg.length < 10) {
        resultDiv.innerHTML = '<p style="color:red;">Please fill in all fields correctly. Message must be at least 10 characters.</p>';
        return;
      }
      let subject = encodeURIComponent(ctype + ' from ' + cname);
      let body = encodeURIComponent('Name: ' + cname + '\nEmail: ' + cemail + '\nType: ' + ctype + '\n\nMessage:\n' + cmsg);
      let mailtoLink = 'mailto:info@capetowncupcakes.co.za?subject=' + subject + '&body=' + body;
      resultDiv.innerHTML = '<p style="color:green;">Your message is ready. Your email app will open to send to info@capetowncupcakes.co.za</p>';
      window.location.href = mailtoLink;
    });
  }
  if (document.getElementById('map')) {
    let map = L.map('map').setView([-33.9249, 18.4241], 13);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: 'Map data &copy; OpenStreetMap contributors'
    }).addTo(map);
    L.marker([-33.9249, 18.4241]).addTo(map)
      .bindPopup('Cape Town Cupcakes<br>123 Main Street, Cape Town')
      .openPopup();
  }
});