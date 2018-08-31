require 'httparty'
require 'json'
require 'pp'
require './lib/roadmap'

class Kele
  include HTTParty
  include Roadmap

  base_uri 'https://www.bloc.io/api/v1'

  def initialize(email, password)
    response = self.class.post('/sessions',
      { body: { email: email, password: password } })
    @auth_token = response["auth_token"]
    raise "Your credentials are invalid.  Please try again." if @auth_token.nil?
  end

  def get_me
    response = self.class.get('/users/me', headers: { "authorization" => @auth_token })
    JSON.parse(response.body)
  end

  def get_mentor_availability(mentor_id)
    response = self.class.get("/mentors/#{mentor_id}/student_availability", headers: { "authorization" => @auth_token })
    open_slots = []
    JSON.parse(response.body).each do |available|
      if available["booked"] == nil
        open_slots << available
      else
        open_slots << "There are no available time slots."
      end
    end
    open_slots
  end

  def get_messages(page = 0)
    if page > 0
      message_url = "/message_threads?page=#{page}"
    else
      message_url = "/message_threads"
    end
    response = self.class.get(message_url, headers: { "authorization" => @auth_token })
    JSON.parse(response.body)
  end

  def create_message(sender, recipient_id, subject, stripped_text, token = nil)
    response = self.class.post("/messages", headers: { "authorization" => @auth_token }, body: {
      sender: sender,
      recipient_id: recipient_id,
      token: token,
      subject: subject,
      stripped_text: stripped_text
      })
  end

  def get_remaining_checkpoints(chain_id)
    response = self.class.get("/enrollment_chains/#{chain_id}/checkpoints_remaining_in_section", headers: { "authorization" => @auth_token })
    JSON.parse(response.body)
  end
end
