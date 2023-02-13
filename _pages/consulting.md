---
layout: page
title: Remote Assistance and Consulting
permalink: /consulting
---

With substantial knowledge and experience in the field of Power BI visual development, [Daniel](about) is recognised in the Power BI community as a Microsoft MVP and Power BI Community Super User. He offers free and open-source custom visuals for Power BI through AppSource, including the versatile <a href="https://deneb-viz.github.io" target="_blank">Deneb</a>, which allows for the creation of custom visuals within Power BI using the <a href="https://vega.github.io/vega/" target="_blank">Vega</a> and <a href="https://vega.github.io/vega-lite" target="_blank">Vega-Lite</a> languages.

If you need one-on-one assistance with your Power BI custom visual development projects, Daniel is available for remote consulting to offer support, provide a second opinion, or help bring your ideas to life. Browse the options below to find the service that meets your needs, and book directly using the provided link. Meetings are held through Microsoft Teams.

<div class="row justify-content-between consult-row consult-row-first">
    <div class="col-md-3">
        <div class="meeting-title">
            Introductory Call (30 min.)
        </div>
    </div>
    <div class="col-md-7">
        <div>This is a complimentary 30-minute initial consultation where we can meet and discuss potential options for assistance, should you have any specific needs or goals you wish to implement or get support on.</div>
        <div class="consult-disclaimer"><small class="text-muted">Introductory calls are limited to <b>one per customer</b>. For additional assistance, please <a href="" onclick="Calendly.initPopupWidget({url: 'https://calendly.com/dm-p/one-hour-consultation?hide_landing_page_details=1&primary_color=ce6c47'});return false;">book a <i>One-hour Consultation</i> session</a>.</small></div>
    </div>
    <div class="col-md-2 consult-book-link">
        <!-- Calendly link widget begin -->
        <link href="https://assets.calendly.com/assets/external/widget.css" rel="stylesheet">
        <script src="https://assets.calendly.com/assets/external/widget.js" type="text/javascript" async></script>
        <a class="btn btn-primary book-button" role="button" href="" onclick="Calendly.initPopupWidget({url: 'https://calendly.com/dm-p/initial-qna-30?hide_landing_page_details=1'});return false;">Book online</a>
        <!-- Calendly link widget end -->
    </div>
</div>

<div class="row justify-content-between consult-row consult-row-final">
    <div class="col-md-3">
        <div class="meeting-title">
            One-hour Consultation
        </div>
    </div>
    <div class="col-md-7">
        The one-hour consultation session provides dedicated assistance with your Power BI visual development projects, using the visual SDK or bespoke visual designers such as Deneb or Charticulator. If you're not certain if this service is suitable for your needs and we are not yet acquainted, it is recommended to schedule a complimentary Introductory Call first to assess feasibility before booking.
    </div>
    <div class="col-md-2 consult-book-link">
        <!-- Calendly link widget begin -->
        <link href="https://assets.calendly.com/assets/external/widget.css" rel="stylesheet">
        <script src="https://assets.calendly.com/assets/external/widget.js" type="text/javascript" async></script>
        <a class="btn btn-primary book-button" role="button" href="" onclick="Calendly.initPopupWidget({url: 'https://calendly.com/dm-p/one-hour-consultation?hide_landing_page_details=1&primary_color=ce6c47'});return false;">Book online</a>
        <!-- Calendly link widget end -->
    </div>

</div>

###### Other Consulting Options

For potentially larger or ongoing engagements, it's recommended to reach out to discuss your requirements before booking. To get in touch, you can send <a target="\_top" href="mailto:{{ site.authors.daniel.email }}?subject=Consulting Enquiry">an email</a> or use the contact form below. Please provide as much information as possible about your project, including your industry and desired outcomes. If you have specific technical or data requirements, please include them in your message to help us determine the best way to support you.

<form action="https://smartforms.dev/submit/63e96bb40dd8ac0a531fe31b" method="POST" class="contact-form" novalidate>
    <div class="form-group">
        <div class="form-row">
            <div class="col">
                <label for="contact-form-name">Your name</label>
                <input type="text" class="form-control" id="contact-form-name" name="Name" placeholder="Enter your name" required>
                <div class="invalid-feedback">
                    Please enter your name
                </div>
            </div>
            <div class="col">
                <label for="contact-form-email">Your email address</label>
                <input type="email" class="form-control" id="contact-form-email" name="Email" aria-describedby="contact-form-email-help" placeholder="Enter your email address" required>
                <small id="contact-form-email-help" class="form-text text-muted">We'll never share your email with anyone else.</small>
                <div class="invalid-feedback">
                    Please provide your email address
                </div>
            </div>
        </div>
    </div>
    <div class="form-group">
        <label for="contact-form-enquiry">Details of your enquiry</label>
        <textarea class="form-control" id="contact-form-enquiry" name="Enquiry" rows="5"></textarea>
    </div>
    <div class="g-recaptcha" data-sitekey="6Ld8v3YkAAAAABKoxdQ9aSWvTHUpyFmdjO52sG8F"></div>
    <button type="submit" class="btn btn-primary contact-form-submit">Submit</button>
</form>
<script src="https://www.google.com/recaptcha/api.js"></script>
<script>
// Example starter JavaScript for disabling form submissions if there are invalid fields
(function() {
  'use strict';
  window.addEventListener('load', function() {
    // Fetch all the forms we want to apply custom Bootstrap validation styles to
    var forms = document.getElementsByClassName('contact-form');
    // Loop over them and prevent submission
    var validation = Array.prototype.filter.call(forms, function(form) {
      form.addEventListener('submit', function(event) {
        if (form.checkValidity() === false) {
          event.preventDefault();
          event.stopPropagation();
        }
        form.classList.add('was-validated');
      }, false);
    });
  }, false);
})();
</script>
