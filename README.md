# eventmachine_httpserver

## EM::HttpServer vs Thin

     +careo | is there a 25 word version of how it differs from thin?
      +tmm1 | good question.. its probably faster, but only because it doesn't have a real http parser like thin
    +wyhaines | It is faster.  It's more of an nginx style parser than the mongrel, grammar based parser.
     +careo | so perhaps a better fit for putting an http interface on top of a bunch of already-evented code?
    +wyhaines | careo: It depends.  There are very valid arguments for using an RFC-compliant parser a lot of the time.
    +wyhaines | But, yeah, sometimes being strictly RFC compliant isn't something that you care about.

## Installing 

### Without Bundler
    sudo gem install eventmachine_httpserver

### With bundler

    gem 'eventmachine_httpserver', :require => 'evma_httpserver'

## Usage example
    require 'eventmachine'
    require 'evma_httpserver'

    class MyHttpServer < EM::Connection
      include EM::HttpServer

       def post_init
         super
         no_environment_strings
       end

      def process_http_request
        # the http request details are available via the following instance variables:
        #   @http_protocol
        #   @http_request_method
        #   @http_cookie
        #   @http_if_none_match
        #   @http_content_type
        #   @http_path_info
        #   @http_request_uri
        #   @http_query_string
        #   @http_post_content
        #   @http_headers

        response = EM::DelegatedHttpResponse.new(self)
        response.status = 200
        response.content_type 'text/html'
        response.content = '<center><h1>Hi there</h1></center>'
        response.send_response
      end
    end

    EM.run{
      EM.start_server '0.0.0.0', 8080, MyHttpServer
    }
